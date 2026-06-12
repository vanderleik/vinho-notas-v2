### PRD: Vinho Notas BFF

Versão: 2.0

Data: 2026-06-12

Responsável: Vanderlei Kleinschmidt

---

### Resumo

O BFF do Vinho Notas é o ponto único de entrada da plataforma para o frontend Angular 19 com Micro Frontends. Ele centraliza o versionamento das rotas externas, valida a identidade do usuário, aplica proteção de tráfego e compõe respostas agregadas para telas que dependem de mais de um microsserviço.

Na prática, essa camada reduz o acoplamento do frontend aos serviços internos, evita chamadas diretas para os microsserviços de domínio e padroniza observabilidade, resiliência e contratos HTTP em um único lugar. O resultado esperado é uma integração mais estável entre Shell, MFEs e backend, com latência previsível, segurança centralizada e evolução controlada das APIs.

---

### Contexto e problema

Público-alvo

- Equipes de frontend que mantêm o Shell e os MFEs e precisam de um ponto único para consumir a plataforma.
- Equipes de backend responsáveis pelos microsserviços de domínio, que dependem de contratos estáveis e versionados.
- Time de plataforma e operações, que precisa de observabilidade, controle de tráfego e diagnósticos consistentes.

Cenários de uso chave

- O Shell e os MFEs consomem apenas o BFF para autenticar, navegar e carregar telas sem conhecer URLs internas.
- O BFF agrega múltiplas respostas para o dashboard e outras telas compostas, reduzindo o número de chamadas do frontend.
- O gateway aplica validação de JWT, limitação de taxa e circuit breaker antes de atingir os serviços de domínio.

Onde essa feature será implantada

- No projeto existente backend/bff, como BFF e API Gateway da plataforma Vinho Notas v2.
- Em desenvolvimento, rodando junto ao ecossistema local com Docker Compose.
- Em produção, executado em contêineres compatíveis com Kubernetes, com exposição externa apenas das rotas /api/v1/*.

Problemas priorizados

- Acoplamento do frontend aos microsserviços internos e risco de chamadas diretas fora do contrato canônico. Impacto: alto, Prioridade: alta.
- Falta de um ponto central de validação de identidade, autorização e propagação de contexto. Impacto: alto, Prioridade: alta.
- Latência e instabilidade em telas que dependem de múltiplos serviços ao mesmo tempo. Impacto: alto, Prioridade: alta.
- Baixa visibilidade operacional quando algo falha entre o frontend e os serviços de domínio. Impacto: médio, Prioridade: média.

---

### Objetivos e métricas

| Objetivo | Métrica | Meta |
| --- | --- | --- |
| Centralizar o consumo do frontend em uma única borda | Percentual de chamadas de API do frontend que passam pelo BFF | 100% |
| Manter resposta previsível nas rotas mais usadas | Latência p95 das rotas de proxy simples do BFF | Menor que 150 ms, hipótese |
| Proteger a plataforma contra falhas e picos de tráfego | Uptime mensal das rotas externas | 99,9%, hipótese |
| Garantir rastreabilidade ponta a ponta | Percentual de requisições com traceId e spanId correlacionados | 100% |
| Evitar dependência direta dos MFEs nos serviços internos | Percentual de chamadas externas expostas apenas em /api/v1/* | 100% |

---

### Escopo

Incluso

- Exposição de rotas externas versionadas em /api/v1/*.
- Validação de JWT, autorização por papel e propagação de identidade para os microsserviços.
- Roteamento para serviços internos e agregação de respostas para telas compostas.
- Limitação de taxa, timeout, retry e circuit breaker por rota.
- Logs estruturados, métricas operacionais e tracing distribuído.
- Cache de respostas agregadas quando aplicável, especialmente para o dashboard.

Fora de escopo

- Regras de negócio dos microsserviços de domínio.
- Persistência de dados funcionais do usuário no BFF.
- Interfaces visuais do frontend, incluindo layout, navegação e componentes visuais.
- IA generativa, notificações, gamificação e demais responsabilidades de domínio fora da borda.
- Acesso direto do frontend aos serviços internos sem passar pelo BFF.

---

### Requisitos funcionais

#### F01 Roteamento canônico e versionamento de API

O sistema deve expor todas as rotas externas apenas sob o prefixo /api/v1/* e encaminhar cada requisição para o serviço interno correto, preservando método HTTP, query string e corpo da requisição.

**Fluxo principal**

- O frontend chama uma rota do BFF em /api/v1/*.
- O gateway identifica a versão, localiza a rota canônica e encaminha a requisição para o serviço de destino.
- A resposta volta ao frontend sem expor URLs internas nem detalhes da topologia.

**Fluxos alternativos e exceções**

- Se a rota vier sem o prefixo /api/v1, o gateway rejeita a chamada antes de atingir qualquer serviço.
- Se a rota existir, mas estiver desativada por configuração, o gateway retorna erro controlado e documentável.
- Se houver mudança de contrato, a rota antiga permanece disponível apenas durante a janela de compatibilidade definida.

**Erros previstos**

- Versão de API não suportada.
- Rota inexistente ou desabilitada.
- Método HTTP incompatível com a rota publicada.

**Prioridade:** alta

---

#### F02 Autenticação JWT e propagação de identidade

O sistema deve validar o JWT em todas as rotas protegidas, bloquear requisições inválidas e propagar para os serviços downstream apenas a identidade e os papéis autorizados do usuário.

**Fluxo principal**

- A requisição entra no BFF com o token de acesso.
- O gateway valida assinatura, expiração e consistência do token.
- O BFF injeta os cabeçalhos de identidade necessários e encaminha a chamada ao serviço de destino.
- O serviço downstream recebe apenas os dados mínimos para autorização e rastreio.

**Fluxos alternativos e exceções**

- Se a rota for pública, a validação de JWT é dispensada para aquela chamada específica.
- Se o papel do usuário não for compatível, o gateway bloqueia a requisição sem chamar o serviço downstream.
- Se o token estiver expirado, o frontend recebe uma resposta clara para iniciar novo login.

**Erros previstos**

- Token ausente, inválido ou expirado.
- Assinatura inválida ou token malformado.
- Papel insuficiente para a rota solicitada.

**Prioridade:** alta

---

#### F03 Resiliência e proteção de tráfego

O sistema deve aplicar limitação de taxa, timeout, retry controlado e circuit breaker para evitar que falhas ou picos em serviços downstream se espalhem para o restante da plataforma.

**Fluxo principal**

- A requisição autenticada entra no gateway.
- O BFF verifica a política da rota e aplica a proteção configurada.
- Se o serviço downstream responder dentro do limite, a resposta é devolvida normalmente.
- Se houver falha repetida, o circuit breaker abre e o BFF interrompe novas tentativas até a recuperação.

**Fluxos alternativos e exceções**

- Se a taxa de requisições ultrapassar o limite configurado, o BFF responde com bloqueio temporário e orienta nova tentativa.
- Se o serviço downstream ficar lento, o timeout impede que a chamada fique pendurada.
- Se houver indisponibilidade parcial, o BFF devolve erro controlado sem derrubar as demais rotas.

**Erros previstos**

- Limite de requisições excedido.
- Timeout de serviço downstream.
- Circuit breaker aberto.
- Falha transitória após esgotar as tentativas configuradas.

**Prioridade:** alta

---

#### F04 Agregação de respostas e dashboard

O sistema deve compor respostas agregadas para telas que dependem de múltiplos microsserviços, especialmente o dashboard, reunindo os dados em uma única chamada e reutilizando snapshot em cache quando disponível.

**Fluxo principal**

- O frontend solicita o dashboard ao BFF.
- O BFF chama os serviços necessários em paralelo e monta a resposta consolidada.
- Se existir snapshot válido em cache, o gateway o utiliza para reduzir latência.
- O frontend recebe um payload único com os blocos necessários para renderização da tela.

**Fluxos alternativos e exceções**

- Se uma fonte não crítica falhar, o BFF mantém a resposta com os dados disponíveis e sinaliza degradação controlada.
- Se não houver cache e algum serviço essencial falhar, o BFF retorna erro claro para o frontend tratar a indisponibilidade.
- Se o snapshot estiver vencido, o BFF recalcula a agregação antes de responder.

**Erros previstos**

- Falha ao compor a resposta agregada.
- Expiração ou ausência de snapshot em cache.
- Indisponibilidade de um serviço necessário à montagem da tela.

**Prioridade:** média

---

#### F05 Observabilidade e operação

O sistema deve registrar logs estruturados, métricas por rota e tracing distribuído em todas as requisições relevantes, permitindo diagnóstico rápido de falhas, latência e comportamento anormal.

**Fluxo principal**

- Cada requisição recebe um identificador de correlação.
- O BFF registra logs em JSON com contexto suficiente para auditoria técnica.
- As métricas de latência, taxa de erro e disponibilidade por rota são publicadas continuamente.
- O tracing é propagado para os serviços downstream e consolidado no observability stack.

**Fluxos alternativos e exceções**

- Se a exportação de métricas falhar, o tráfego principal continua operando.
- Se o tracing estiver indisponível, o BFF mantém o processamento e degrada apenas a telemetria.
- Se um erro crítico ocorrer, o BFF registra o evento com severidade apropriada para investigação.

**Erros previstos**

- Falha de exportação de métricas.
- Perda de correlação entre chamadas síncronas.
- Logs incompletos em rotas críticas.

**Prioridade:** média

---

### Requisitos não funcionais

Performance

- p95 menor que 150 ms para rotas simples de proxy, hipótese.
- p95 menor que 300 ms para respostas agregadas do dashboard com cache aquecido, hipótese.
- Tempo de resposta controlado por timeout por rota para evitar chamadas penduradas.

Disponibilidade

- 99,9 por cento de uptime mensal para as rotas externas, hipótese.
- Reinício e recuperação sem perda do contrato externo publicado.

Segurança e autorização

- Autenticação obrigatória nas rotas protegidas.
- Autorização por papel em rotas sensíveis.
- Propagação apenas de identidade mínima necessária para os serviços downstream.
- Nenhum segredo, senha ou token sensível deve aparecer em logs.

Observabilidade

- Logs estruturados em JSON.
- traceId e spanId em todas as requisições relevantes.
- Métricas de latência, erro por rota e circuit breaker por serviço downstream.
- Dashboard operacional com alertas de erro e degradação de cache.

Confiabilidade e integridade de dados

- O BFF não armazena estado de negócio permanente.
- Chamadas agregadas devem falhar de forma controlada, sem corromper dados nos serviços de origem.
- Política de timeout, retry e fallback deve impedir cascata de falhas.

Compatibilidade e portabilidade

- Contratos externos devem permanecer em REST JSON versionado sob /api/v1/*.
- A aplicação deve ser empacotada como contêiner OCI.
- O consumo do frontend deve permanecer exclusivo pelo BFF.

Compliance

- O BFF deve respeitar a LGPD ao limitar o tráfego de dados pessoais ao mínimo necessário.
- Alterações sensíveis e acessos relevantes devem deixar trilha técnica suficiente para auditoria.

Acessibilidade no frontend consumidor

- As respostas do BFF devem ser consistentes e previsíveis para permitir tratamento acessível no frontend, com códigos HTTP claros e mensagens objetivas.

---

### Arquitetura e abordagem

Abordagem

- BFF e API Gateway centralizado, construído sobre Spring Cloud Gateway, responsável por validação de JWT, roteamento, limitação de tráfego, agregação de respostas e observabilidade.
- Comunicação síncrona por HTTPS/REST entre frontend, BFF e microsserviços de domínio.
- Cache e proteção de tráfego apoiados por Redis.
- Resiliência de chamadas suportada por Resilience4j.

Componentes

- Spring Cloud Gateway como ponto único de entrada.
- Spring Security para validação de autenticação e autorização.
- WebFlux e Netty para processamento reativo e concorrente.
- Redis para rate limiting e cache de respostas agregadas.
- Micrometer e Actuator para telemetria e saúde operacional.
- Serviços downstream: svc-autenticacao, svc-perfil, svc-vinho, svc-adega, svc-avaliacao, svc-degustacao, svc-ia, svc-gamificacao, svc-notificacao e svc-analytics.

Integrações

- Frontend Angular 19 com Shell e MFEs, que consome apenas o BFF.
- Microsserviços de domínio expostos internamente por REST.
- Redis para cache e controle de taxa.
- Stack de observabilidade com Prometheus, Grafana e tracing distribuído.

### Decisões e trade-offs

#### Decisão: Expor apenas /api/v1/* como contrato externo

- **Justificativa:** reduz ambiguidade de contrato, facilita evolução e evita quebra silenciosa de integrações do frontend.
- **Trade-off:** exige disciplina de versionamento e coordenação quando surgirem mudanças incompatíveis.

#### Decisão: Validar JWT no BFF antes de qualquer chamada downstream

- **Justificativa:** centraliza a segurança, reduz repetição de lógica nos serviços e impede chamadas indevidas desde a borda.
- **Trade-off:** o BFF passa a ser mais sensível a falhas de autenticação e precisa ser altamente disponível.

#### Decisão: Usar Redis para proteção de tráfego e cache de dashboard

- **Justificativa:** reduz latência e evita pressão desnecessária sobre os serviços de origem.
- **Trade-off:** adiciona uma dependência operacional extra e exige cuidado com TTL e invalidação.

#### Decisão: Compor respostas agregadas no BFF

- **Justificativa:** simplifica o frontend e reduz o número de chamadas por tela.
- **Trade-off:** aumenta a complexidade da borda e cria pontos de atenção em timeout, fallback e observabilidade.

---

### Dependências

#### technical: Redis operacional e configurado

O BFF depende de Redis para rate limiting, proteção de tráfego e cache de snapshot do dashboard. Sem essa base, as políticas de resiliência e cache ficam incompletas.

#### external: Contratos dos microsserviços de domínio

Os serviços svc-autenticacao, svc-perfil, svc-vinho, svc-adega, svc-avaliacao, svc-degustacao, svc-ia, svc-gamificacao, svc-notificacao e svc-analytics precisam publicar contratos estáveis para que o BFF consiga encaminhar e agregar respostas sem quebra de integração.

#### organizational: Migração do frontend para o contrato canônico

As equipes responsáveis pelo Shell e pelos MFEs precisam consumir somente o BFF e aderir ao padrão /api/v1/* para que a mudança não fique parcialmente aplicada.

---

### Riscos e mitigação

#### Deriva entre contratos do BFF e os serviços downstream

- **Probabilidade:** alta
- **Impacto:** alto, porque quebra as telas que dependem de agregação e roteamento.
- **Mitigação:**
    - Versionamento explícito das rotas e validação de contrato em ambiente de integração.
    - Revisão coordenada entre frontend, BFF e times de serviço antes de mudanças incompatíveis.
- **Plano de contingência:** manter a versão anterior da rota durante o período de transição e realizar rollback do endpoint afetado.

#### Falha em cascata causada por lentidão dos serviços de domínio

- **Probabilidade:** média
- **Impacto:** alto, principalmente nas telas agregadas.
- **Mitigação:**
    - Timeouts curtos por rota.
    - Circuit breaker e retry controlado.
    - Uso de cache para respostas agregadas sempre que possível.
- **Plano de contingência:** servir snapshot em cache ou degradar a tela com dados parciais e mensagem de indisponibilidade.

#### Bloqueio indevido de usuários válidos por configuração errada de auth ou rate limit

- **Probabilidade:** média
- **Impacto:** alto, porque impede uso legítimo da plataforma.
- **Mitigação:**
    - Políticas por rota com valores revisados em homologação.
    - Monitoramento de 401, 403 e 429 com alertas.
    - Testes de fumaça antes de promover mudanças de configuração.
- **Plano de contingência:** liberar temporariamente a política da rota afetada e restaurar a configuração anterior.

#### Falta de visibilidade operacional durante incidentes

- **Probabilidade:** média
- **Impacto:** médio, porque aumenta o tempo de diagnóstico.
- **Mitigação:**
    - Logs estruturados com traceId e spanId.
    - Métricas por rota e alertas em Grafana.
    - Propagação de correlação para os serviços downstream.
- **Plano de contingência:** habilitar nível de log mais detalhado nas rotas críticas até o incidente ser isolado.

---

### Critérios de aceitação

Checklist objetivo que define se a feature está pronta.

- Todas as rotas externas do frontend respondem apenas sob /api/v1/*.
- Requisições sem JWT válido recebem 401 antes de qualquer chamada downstream.
- Requisições com papel insuficiente recebem 403 e não alcançam o serviço de destino.
- Rotas com proteção de tráfego retornam 429 quando o limite configurado é excedido.
- O dashboard é entregue por uma única chamada agregada e respeita o cache de snapshot com TTL de 1 hora.
- As respostas críticas carregam traceId, spanId e identificador técnico da rota nos logs.
- A infraestrutura de observabilidade mostra latência, taxa de erro e estado de circuit breaker por rota.
- O frontend não precisa conhecer URLs internas de microsserviços para funcionar em produção.

---

### Testes e validação

Tipos de teste obrigatórios

- Testes unitários para regras de roteamento, validação de token e políticas de autorização.
- Testes de integração com chamadas reais ao menos dos fluxos de proxy, auth e dashboard.
- Testes de contrato para garantir compatibilidade entre o BFF e os microsserviços downstream.
- Testes de resiliência para timeout, retry, circuit breaker e rate limiting.
- Testes de observabilidade para verificar correlação de logs, métricas e tracing.

Estratégia de validação

- TDD para regras críticas de segurança e roteamento.
- Validação de integração com ambiente local e homologação usando contratos estáveis.
- QA manual guiado por roteiro nas telas mais dependentes de agregação, especialmente o dashboard.
- Validação exploratória em cenários de falha parcial para confirmar comportamento degradado e mensagens controladas.