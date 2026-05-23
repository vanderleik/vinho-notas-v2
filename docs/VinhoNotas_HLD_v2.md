### HLD: Vinho Notas

Versão: 2.0

Data: 2026-05-23

Responsável: Vanderlei Kleinschmidt

---

### Objetivo técnico

Estruturar uma plataforma PWA para registro, avaliação e descoberta de vinhos com separação clara entre frontend, BFF e microsserviços de domínio. O principal problema técnico resolvido é o acoplamento do MVP anterior, substituído por uma arquitetura distribuída com isolamento de dados por serviço, integração assíncrona para efeitos colaterais e composição de respostas no BFF.

Dependências com outros sistemas

- Provedores de autenticação social Google e Apple.
- API de IA generativa para harmonização, chat e recomendações.
- RabbitMQ para eventos de domínio e notificações assíncronas.
- Redis para rate limiting, cache de dashboard e contexto de chat.

---

### Arquitetura geral

A solução adota uma topologia híbrida: em produção, serviços cloud-native com contêineres e orquestração prevista em Kubernetes; em desenvolvimento, Docker Compose como ambiente local. O frontend é um PWA em Angular 19 com Micro Frontends via Native Federation, organizado em Shell e remotes por domínio. O backend usa Java 21, Spring Boot 3.3, Clean Architecture e Spring Cloud Gateway incorporado ao BFF. A comunicação síncrona ocorre via HTTPS/REST entre frontend, BFF e serviços; a comunicação assíncrona usa RabbitMQ com Outbox Pattern e Saga por coreografia.

Ambiente de implantação

- Híbrido.
- Desenvolvimento com Docker Compose e serviços locais de apoio.
- Produção com serviços containerizados, escaláveis horizontalmente e compatíveis com Kubernetes.

Tecnologias principais

- Angular 19.
- TypeScript 5.5+.
- Native Federation.
- Java 21.
- Spring Boot 3.3.x.
- Spring Cloud Gateway 4.1.x.
- Spring Security e JWT.
- RabbitMQ 3.13.
- PostgreSQL 16.
- Redis 7.2.
- Micrometer, Actuator e Micrometer Tracing.
- Resilience4j.
- Docker e Docker Compose.

Padrões adotados

- Micro Frontends.
- PWA com Service Worker.
- BFF.
- Clean Architecture.
- Event-driven architecture.
- Outbox Pattern.
- Saga por coreografia.
- Stateless services.
- Twelve-Factor App.

---

### Componentes e responsabilidades

| Componente | Responsabilidades | Dependências |
| --- | --- | --- |
| Shell Angular | Roteamento de topo, carregamento lazy dos remotes, autenticação de sessão, registro do Service Worker | shared-auth, shared-state, BFF, Native Federation |
| mfe-autenticacao | Login, cadastro, recuperação de senha e perfil de acesso | BFF, shared-auth |
| mfe-vinhos | Cadastro, consulta, scan de rótulo, wishlist e adega visual | BFF, shared-models |
| mfe-degustacao | Avaliação rápida, ficha formal, degustação comparativa e exportação | BFF, shared-models |
| mfe-sommelier | Chat com IA, harmonização e recomendações | BFF, svc-ia |
| mfe-dashboard | Resumo, gráficos, ranking e indicadores do usuário | BFF, svc-analytics |
| mfe-perfil | Dados pessoais, preferências, notificações, conquistas e nível | BFF, svc-perfil, svc-gamificacao |
| BFF / API Gateway | Validação JWT, rate limiting, circuit breaker, routing, agregação de respostas e propagação de identidade | Spring Cloud Gateway, Redis, Resilience4j, todos os serviços downstream |
| svc-autenticacao | Cadastro, login, OAuth, refresh token e emissão de JWT | PostgreSQL, OAuth Google/Apple |
| svc-perfil | Preferências de idioma, tema, pontuação e notificações | PostgreSQL |
| svc-vinho | Catálogo do acervo e wishlist | PostgreSQL, RabbitMQ, outbox |
| svc-adega | Quantidade, posição física e cálculo de ponto ideal de consumo | PostgreSQL, svc-vinho, RabbitMQ, scheduler |
| svc-avaliacao | Avaliação rápida e histórico | PostgreSQL, RabbitMQ, outbox |
| svc-degustacao | Fichas formais, etapas sensoriais e degustações comparativas | PostgreSQL, JSONB, RabbitMQ, outbox |
| svc-ia | Harmonização, chat e recomendações personalizadas | PostgreSQL, Redis, provedor de LLM |
| svc-gamificacao | Níveis, badges e progresso do usuário | PostgreSQL, RabbitMQ |
| svc-notificacao | Agendamento, entrega push/e-mail e histórico de envio | PostgreSQL, RabbitMQ, PWA Service Worker, e-mail provider |
| svc-analytics | Snapshot de dashboard, ranking, gasto e leitura agregada | PostgreSQL, Redis, RabbitMQ |
| RabbitMQ | Transporte de eventos de domínio, filas duráveis e DLQ | Producers e consumers de todos os serviços de domínio |
| PostgreSQL por serviço | Persistência isolada com ownership por domínio | Cada microsserviço é dono exclusivo do seu banco |
| Redis | Rate limiting, cache de chat e cache de dashboard | BFF, svc-ia, svc-analytics |

---

### Fluxo de requisições e de dados

**Fluxo de requisição**

- O usuário acessa o Shell, que carrega o MFE correto via Native Federation.
- O MFE envia a requisição ao BFF com JWT no header.
- O BFF valida o token, aplica rate limiting e roteia a chamada ao serviço correto.
- Quando necessário, o BFF agrega múltiplos serviços em paralelo para montar a resposta.
- A resposta volta ao frontend já otimizada para a tela em uso.

**Fluxo de dados**

- Cadastro ou atualização em um serviço persiste primeiro no PostgreSQL do próprio domínio.
- O mesmo fluxo registra um evento na tabela de outbox da transação.
- Um relay publica o evento no RabbitMQ após o commit.
- Consumidores atualizam analytics, gamificação e notificações de forma assíncrona.
- O dashboard lê snapshots prontos do svc-analytics, com cache em Redis quando aplicável.

---

### Modelo de dados (alto nível)

Entidades principais

- Usuario.
- Perfil.
- ConfiguracaoNotificacao.
- Vinho.
- Wishlist.
- ItemAdega.
- AlertaConsumo.
- Avaliacao.
- Degustacao.
- ItemDegustacao.
- FichaDegustacao.
- HistoricoChat.
- Recomendacao.
- Harmonizacao.
- Nivel.
- Conquista.
- ProgressoUsuario.
- ConquistaUsuario.
- NotificacaoAgendada.
- SnapshotUsuario.
- GastoMensal.

Relações

- Um usuario possui perfis, avaliações, degustações, vinhos, itens de adega e preferências.
- Um vinho pode aparecer em avaliações, degustações, adega e wishlist, mas sempre por referência UUID entre serviços.
- Uma degustacao possui vários itens de degustacao e uma ou mais fichas de degustacao.
- Um item de adega gera alertas de consumo.
- Um progresso de usuario acumula conquistas e determina o nível atual.
- Um snapshot de analytics consolida dados de múltiplos serviços para leitura rápida.

Fonte de verdade

- Cada microsserviço é a fonte de verdade do seu próprio domínio e do seu próprio banco PostgreSQL.
- Não existem chaves estrangeiras entre bancos de serviços diferentes.
- Redis funciona como cache ou estado efêmero, nunca como sistema mestre.

---

### Interfaces públicas

| Nome | Tipo | Protocolo | Exposição | SLAs/Limites |
| --- | --- | --- | --- | --- |
| API do BFF | API | HTTPS/REST | Externa | p95 alinhado à tela; dashboard com agregação paralela e timeout por rota |
| APIs internas dos microsserviços | API | HTTP/REST | Interna | chamadas orquestradas pelo BFF; serviços stateless e isolados |
| Topic exchange de eventos | Queue | AMQP/RabbitMQ | Interna | filas duráveis, ack manual, retry até 3 tentativas e DLQ |
| Contexto de chat e snapshot de dashboard | Cache/Estado | Redis | Interna | TTL 24h para chat e 1h para dashboard |

---

### Considerações de escalabilidade e disponibilidade

Abordagem geral

- Escala horizontal independente por serviço, sem acoplamento entre bancos.
- Frontend distribuído em Shell e remotes, com deploy independente por MFE.
- BFF sem estado, com cache e rate limiting no Redis.
- Consistência eventual para fluxos assíncronos, mantendo a experiência principal responsiva.

Técnicas aplicadas

- Load balancing na borda.
- Cache de dashboard e contexto de chat.
- Rate limiting no BFF.
- Circuit breaker, timeout e retry com Resilience4j.
- RabbitMQ com DLQ, ack manual e backoff exponencial.
- Outbox Pattern para entrega garantida de eventos.
- Virtual threads por serviço para maior concorrência por instância.
- Graceful shutdown para drenagem segura de requisições.

Meta de disponibilidade

- Meta inicial adotada como hipótese: 99,9% para interfaces externas e 99,5% para interfaces internas.

---

### Segurança

Autenticação

- JWT validado no BFF antes de qualquer chamada downstream.
- Login por e-mail e senha ou OAuth com Google e Apple.
- Refresh token tratado de forma segura em cookie HTTP-only, com rotação e revogação.

Autorização

- Controle baseado em papéis e perfil do usuário.
- O BFF propaga apenas o identificador de usuário para os serviços downstream.
- Cada serviço valida a identidade e aplica regras de acesso no seu próprio contexto.

Proteção de dados

- Comunicação entre cliente e servidor sempre em HTTPS com TLS 1.2 ou superior.
- Senhas armazenadas com bcrypt ou Argon2.
- Dados pessoais seguem a LGPD, com suporte a exportação e exclusão.
- Logs não devem expor PII nem segredos.

Gestão de segredos

- Configuração por variáveis de ambiente.
- Segredos externos devem ser mantidos em vault ou serviço equivalente em ambiente produtivo.

---

### Observabilidade

Logs

- Logs estruturados em JSON no stdout para todas as operações críticas.
- Identificadores de correlação como traceId, spanId e usuarioId devem estar presentes quando aplicável.

Métricas

- Latência p95 por endpoint.
- Taxa de erro por rota e por serviço.
- Profundidade de filas RabbitMQ e tamanho da DLQ.
- Cache hit ratio do Redis.
- Tempo de processamento do outbox relay.
- Snapshot age do dashboard.

Tracing

- Rastreamento distribuído ponta a ponta com Micrometer Tracing.
- Propagação de trace entre frontend, BFF, microsserviços e consumidores assíncronos.

Dashboards e alertas

- Grafana para visão operacional e de produto.
- Alertas para aumento de latência, falha de consumidores, crescimento da DLQ e degradação do cache.

---

### Riscos arquiteturais e mitigação

#### Consistência eventual entre escrita e processamento assíncrono

- **Probabilidade:** média
- **Impacto:** alto, principalmente em dashboard, gamificação e notificações
- **Mitigação:** Outbox Pattern, consumidores idempotentes, DLQ e reprocessamento controlado
- **Plano de contingência:** replay manual de eventos pendentes e reconstrução de snapshots a partir do histórico

#### Deriva de contratos entre Shell, BFF, microsserviços e eventos

- **Probabilidade:** média
- **Impacto:** alto, por afetar deploy independente dos MFEs e integração entre serviços
- **Mitigação:** versionamento de contratos OpenAPI e eventos, testes de contrato e shared models bem delimitados
- **Plano de contingência:** rollback do MFE ou do serviço afetado e congelamento temporário do contrato

#### Latência e custo do módulo de IA

- **Probabilidade:** média
- **Impacto:** médio, com reflexo direto na experiência do chat e das recomendações
- **Mitigação:** cache no Redis, timeouts, circuit breaker, fallback informativo e leitura de histórico persistido
- **Plano de contingência:** degradar para respostas cacheadas ou desligar parcialmente a IA sem bloquear o resto da plataforma

#### Sincronização offline e conflitos de escrita no PWA

- **Probabilidade:** média
- **Impacto:** médio, principalmente para registros rápidos feitos sem conexão
- **Mitigação:** fila local no IndexedDB, sincronização automática, chaves de idempotência e regras claras de merge
- **Plano de contingência:** solicitar revisão manual do conflito ao usuário quando houver duplicidade ou divergência

---

### ADRs e próximos passos

ADRs associados

- Nenhum ADR formal cadastrado em docs/adrs no momento.

Decisões pendentes

- Consolidar a fonte de verdade para a stack de frontend, porque documentos legados ainda citam React em diagramas históricos.
- Fechar SLAs finais por interface e política de retenção LGPD.
- Formalizar versionamento de contratos OpenAPI e de eventos RabbitMQ.

Próximos passos

- Quebrar este HLD em FDD e LLD por serviço.
- Criar ADRs para frontend, integração assíncrona, observabilidade e persistência.
- Publicar contratos de API e catálogo de eventos.
- Validar os fluxos críticos com testes de contrato e integração.
