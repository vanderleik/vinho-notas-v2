### PRD: Vinho Notas Serviço de Adega

Versão: 2.0

Data: 2026-06-12

Responsável: Vanderlei Kleinschmidt

---

### Resumo

O svc-adega é o serviço responsável por gerenciar a adega virtual do Vinho Notas, controlando quantidade de garrafas por vinho, posição física opcional e cálculo do ponto ideal de consumo. Ele substitui a lógica dispersa do MVP por uma fonte de verdade isolada para estoque de adega, com integração síncrona com o svc-vinho e integração assíncrona com notificação e analytics.

O serviço existe para que usuários como Ricardo e André consigam organizar a adega, mover itens da wishlist para o estoque e receber alertas confiáveis quando um vinho entrar no período ideal de consumo. A solução foi desenhada para operar no backend Java 21 do ecossistema Vinho Notas, com PostgreSQL, RabbitMQ e scheduler diário.

---

### Contexto e problema

Público-alvo

- Enófilos que mantêm adega doméstica e precisam controlar estoque, posição e ponto de consumo.
- Usuários avançados que registram preço pago, organizam inventário e exportam dados para referência.
- Usuários que querem mover vinhos da wishlist para a adega sem redigitar informações já conhecidas.

Cenários de uso chave

- Registrar quantidades de garrafas por vinho e opcionalmente indicar prateleira e coluna.
- Calcular e exibir o intervalo de consumo ideal com base em safra e tempo de guarda.
- Emitir alerta quando o vinho entra no ponto ideal de consumo e encaminhar o evento ao svc-notificacao.
- Converter item da wishlist em item de adega reaproveitando dados já cadastrados.
- Exportar o inventário da adega para CSV ou PDF para backup e referência.

Onde essa feature será implantada

- Backend de microsserviços do Vinho Notas, no serviço `svc-adega`, exposto ao frontend apenas via BFF Spring Cloud Gateway.

Problemas priorizados

- Falta de uma fonte única e confiável para estoque da adega e posição física das garrafas. Impacto: alto. Prioridade: alta.
- Ausência de cálculo automatizado do ponto ideal de consumo, o que faz o usuário perder janelas de abertura. Impacto: alto. Prioridade: alta.
- Redigitação manual ao mover um vinho da wishlist para a adega. Impacto: médio. Prioridade: média.
- Exportação de inventário sem padronização entre telas e relatórios. Impacto: médio. Prioridade: média.

---

### Objetivos e métricas

| Objetivo | Métrica | Meta |
| --- | --- | --- |
| Permitir controle confiável da adega virtual | Percentual de itens de adega persistidos com quantidade válida e vinho associado | 100% dos itens salvos com validação de consistência |
| Automatizar a identificação do ponto ideal de consumo | Percentual de alertas gerados para itens elegíveis | 100% dos itens com safra e tempo de guarda válidos geram cálculo |
| Reduzir esforço para mover itens da wishlist | Tempo médio para converter wishlist em adega | Menor que 1 minuto por item, hipótese |
| Garantir consumo do serviço sem regressão perceptível | Latência p95 das consultas principais do BFF para adega | p95 menor que 150 ms, hipótese técnica padrão |

---

### Escopo

Incluso

- Cadastro e manutenção de itens de adega com quantidade, posição opcional, safra, tempo de guarda, data de compra, preço pago e observações.
- Geração do cálculo de ponto ideal de consumo para vinhos elegíveis.
- Publicação do evento `adega.alerta.consumo` para o svc-notificacao.
- Conversão de item da wishlist em item de adega reaproveitando dados existentes.
- Leitura do inventário para composição de painel e exportação.

Fora de escopo

- Controle de compra e venda de vinhos.
- Alteração de dados mestre do vinho, como rótulo, produtor ou uva, que pertencem ao svc-vinho.
- Gerenciamento de preferências de canal de notificação, que pertence ao svc-perfil e svc-notificacao.
- Geração do conteúdo visual de cards sociais, que pertence ao módulo de compartilhamento.

---

### Requisitos funcionais

#### FR01 Gerenciar itens da adega

O sistema deve permitir registrar e manter itens da adega virtual com quantidade de garrafas, posição física opcional, safra, tempo de guarda, data de compra, preço pago e observações, mantendo o vínculo com o vinho correspondente por UUID.

**Fluxo principal**

- O usuário acessa a adega virtual no frontend.
- O frontend envia ao BFF os dados do item de adega.
- O BFF encaminha a requisição ao svc-adega.
- O serviço valida quantidade maior que zero, vínculo com vinho existente e campos de negócio obrigatórios.
- O serviço persiste o item no PostgreSQL do próprio domínio.
- O serviço retorna o item criado para o painel da adega.

**Fluxos alternativos e exceções**

- A posição física pode ser omitida e o item continua válido.
- O usuário pode editar um item existente para ajustar quantidade, posição, preço pago ou observações.
- O usuário pode excluir um item, desde que a exclusão não viole integridade de alertas já gerados.

**Erros previstos**

- Quantidade menor ou igual a zero deve ser rejeitada com validação.
- Vinho inexistente no serviço de vinho deve impedir o cadastro.
- Falha de persistência no banco deve retornar erro operacional ao BFF.

**Prioridade:** alta

---

#### FR02 Calcular ponto ideal de consumo

O sistema deve calcular e manter o intervalo estimado de consumo ideal para itens de adega que possuam safra e tempo de guarda válidos, identificando quando um vinho entra no período de pico.

**Fluxo principal**

- O usuário cadastra ou atualiza um item com safra e tempo de guarda.
- O svc-adega calcula a janela de início e fim do ponto ideal de consumo.
- O serviço persiste os dados do alerta no banco local.
- Um processo agendado verifica diariamente os alertas elegíveis.
- Quando a data de início do pico é atingida e o alerta ainda não foi enviado, o evento é preparado para notificação.

**Fluxos alternativos e exceções**

- Se safra ou tempo de guarda estiver ausente, o cálculo não é executado e o item permanece sem alerta.
- Se o vinho já estiver fora da janela útil, o sistema pode manter o histórico do alerta, mas não reemitir o evento.

**Erros previstos**

- Dados de safra inconsistentes devem impedir o cálculo.
- Falha na rotina agendada deve preservar o estado do alerta para reprocessamento posterior.
- Reprocessamento duplicado deve ser evitado por controle de `alerta_enviado`.

**Prioridade:** alta

---

#### FR03 Publicar alerta de consumo

O sistema deve publicar o evento de domínio `adega.alerta.consumo` quando um item atingir o ponto ideal de consumo, para que o svc-notificacao envie push, e-mail ou ambos conforme preferência do usuário.

**Fluxo principal**

- O scheduler diário identifica alertas aptos a notificação.
- O svc-adega publica o evento com `usuarioId`, `vinhoId`, rótulo e ponto ideal.
- O RabbitMQ entrega o evento ao svc-notificacao.
- O svc-notificacao verifica o canal preferido do usuário e envia a mensagem.
- O svc-adega marca o alerta como enviado.

**Fluxos alternativos e exceções**

- Se o consumidor de notificação estiver indisponível, o evento permanece na fila durável para retry.
- Se o usuário já recebeu o alerta, o serviço não deve reenviar a mesma notificação.

**Erros previstos**

- Falha ao publicar no RabbitMQ deve manter o alerta pendente para nova tentativa.
- Falha de consumo no svc-notificacao deve gerar reprocessamento via fila e DLQ se necessário.
- Duplicidade de evento deve ser tratada de forma idempotente pelo consumidor.

**Prioridade:** alta

---

#### FR04 Converter wishlist em adega

O sistema deve permitir mover um item da wishlist para a adega virtual, reaproveitando os dados já registrados e solicitando apenas os campos específicos de estoque.

**Fluxo principal**

- O usuário seleciona um vinho da wishlist.
- O frontend solicita a conversão via BFF.
- O svc-adega recebe os dados reaproveitados do item.
- O serviço solicita apenas quantidade e posição física opcional.
- O item é criado na adega e o item de origem pode ser marcado como convertido.

**Fluxos alternativos e exceções**

- Se o item já existir na adega, o sistema pode oferecer atualização da quantidade em vez de criação duplicada.
- Se o vinho não estiver mais na wishlist, a conversão deve ser bloqueada.

**Erros previstos**

- Inconsistência entre wishlist e adega deve ser sinalizada ao usuário.
- Falha na leitura do item de origem deve interromper a conversão.

**Prioridade:** média

---

#### FR05 Exportar inventário da adega

O sistema deve permitir exportar o inventário completo da adega em CSV ou PDF para fins de backup e referência.

**Fluxo principal**

- O usuário solicita a exportação do inventário.
- O frontend envia a requisição ao BFF.
- O BFF consulta o svc-adega e obtém os itens da adega.
- O serviço entrega os dados estruturados para montagem do arquivo.
- O arquivo é gerado no formato solicitado e disponibilizado ao usuário.

**Fluxos alternativos e exceções**

- CSV deve priorizar consumo em planilhas e auditoria rápida.
- PDF deve priorizar leitura humana com identidade visual da plataforma.

**Erros previstos**

- Inventário vazio deve resultar em arquivo vazio ou mensagem orientativa, conforme decisão de interface.
- Falha de geração do arquivo deve retornar erro controlado sem alterar dados de origem.

**Prioridade:** média

---

### Requisitos não funcionais

Performance

- p95 menor que 150 ms para consultas síncronas principais expostas pelo BFF, hipótese alinhada ao padrão da plataforma.
- Rotina diária de cálculo e disparo de alertas deve concluir dentro da janela operacional prevista sem bloquear solicitações do usuário.

Disponibilidade

- 99,9 por cento de disponibilidade para a interface externa da plataforma, com tolerância a falhas do consumidor assíncrono sem bloquear o cadastro da adega.

Segurança e autorização

- JWT validado no BFF antes de qualquer chamada ao svc-adega.
- Acesso restrito ao dono do inventário, com autorização por usuário e contexto.
- Logs não devem expor dados sensíveis como preço pago em texto livre quando não necessário.

Observabilidade

- Logs estruturados em JSON com `traceId`, `spanId` e identificador do usuário quando aplicável.
- Métricas de taxa de criação, atualização, exclusão e falha de alertas devem ser expostas por endpoint.
- Tracing distribuído entre frontend, BFF, svc-adega, RabbitMQ e svc-notificacao.

Confiabilidade e integridade de dados

- A gravação de item de adega e a criação de alerta devem ser transacionais no banco do serviço.
- Eventos de domínio devem seguir Outbox Pattern para evitar perda entre commit e publicação.
- Consumidores devem ser idempotentes e operar com DLQ para reprocessamento.

Compatibilidade e portabilidade

- API interna e externa do ecossistema deve seguir JSON e rotas versionadas em `/api/v1`.
- O serviço deve ser executável em contêiner OCI e em ambiente local com Docker Compose.

Compliance

- O serviço deve preservar histórico mínimo para reconciliação de alertas e inventário.
- Exclusões lógicas devem ser preferidas quando houver impacto em histórico de adega e alertas.

Acessibilidade no frontend consumidor

- As respostas precisam fornecer dados suficientes para que o frontend exiba labels claros, estados e mensagens compreensíveis.

---

### Arquitetura e abordagem

Abordagem

- Microsserviço Java 21 isolado por domínio, responsável por estoque de adega e cálculo de ponto ideal de consumo, com persistência própria em PostgreSQL e integração assíncrona via RabbitMQ para o evento de alerta.

Componentes

- API REST do svc-adega em Spring Boot 3.3.x.
- Banco PostgreSQL `adega_db` como fonte de verdade.
- Scheduler diário para cálculo e publicação de alertas.
- RabbitMQ para publicação do evento `adega.alerta.consumo`.
- Integração síncrona com `svc-vinho` para validar e resolver dados do vinho.
- Consumo do `svc-notificacao` para entrega do alerta ao usuário.
- Integração com `svc-analytics` para atualizar o total da adega em leitura agregada.

Integrações

- `svc-vinho` para obter dados do rótulo e validar vínculos de vinho.
- `svc-notificacao` para entrega de alerta de ponto ideal de consumo.
- `svc-analytics` para atualização de snapshot e totais da adega.
- RabbitMQ como canal assíncrono de eventos de domínio.

### Decisões e trade-offs

#### Decisão: Adega como serviço proprietário do seu próprio banco

- **Justificativa:** mantém isolamento de domínio e evita acoplamento com os demais serviços.
- **Trade-off:** consultas agregadas exigem composição no BFF e aumentam a complexidade operacional.

#### Decisão: Cálculo de alerta com publicação assíncrona via RabbitMQ

- **Justificativa:** permite notificação desacoplada sem bloquear o fluxo principal de cadastro ou edição.
- **Trade-off:** o sistema passa a operar com consistência eventual para notificações e depende de idempotência e DLQ.

#### Decisão: Uso de scheduler diário para varrer alertas elegíveis

- **Justificativa:** reduz custo de processamento contínuo e simplifica a emissão de alertas de consumo.
- **Trade-off:** o alerta pode ser emitido dentro de uma janela diária, e não exatamente no instante do pico.

---

### Dependências

#### technical: svc-vinho como fonte dos dados do rótulo

O svc-adega precisa consultar o svc-vinho para validar o `vinhoId` e exibir os atributos do rótulo quando necessário. Sem isso, o serviço não consegue garantir que o item de adega esteja vinculado a um vinho existente.

#### technical: RabbitMQ e svc-notificacao

O evento `adega.alerta.consumo` depende de RabbitMQ para transporte confiável e do svc-notificacao para efetivamente entregar o lembrete ao usuário.

#### technical: BFF como ponto de entrada

O frontend não acessa o svc-adega diretamente. O BFF precisa expor as rotas versionadas e orquestrar as chamadas síncronas.

#### organizational: definição de templates de exportação

A exportação em PDF e a apresentação do painel dependem de identidade visual e layout já alinhados com o restante da plataforma.

---

### Riscos e mitigação

#### Cálculo incorreto do ponto ideal de consumo gerar alerta fora da janela esperada

- **Probabilidade:** média
- **Impacto:** alto
- **Mitigação:**
    - Validar regras de cálculo com cenários representativos de safra e tempo de guarda.
    - Cobrir o algoritmo com testes unitários e de integração.
    - Registrar os dados-base usados no cálculo para auditoria e revisão.
- **Plano de contingência:** desativar temporariamente a emissão automática e permitir revisão manual do alerta.

#### Evento de alerta duplicado ser entregue ao usuário

- **Probabilidade:** média
- **Impacto:** alto
- **Mitigação:**
    - Controlar `alerta_enviado` no banco do svc-adega.
    - Exigir consumidores idempotentes no svc-notificacao.
    - Usar DLQ e reprocessamento monitorado.
- **Plano de contingência:** deduplicar na leitura do histórico de notificações e bloquear reenvio manual do mesmo alerta.

#### Falha de integração com svc-vinho impedir cadastro ou edição do item

- **Probabilidade:** média
- **Impacto:** médio
- **Mitigação:**
    - Aplicar timeout e retry controlado na chamada síncrona.
    - Exibir mensagem clara de indisponibilidade no frontend.
    - Permitir retentativa sem perda dos dados já digitados.
- **Plano de contingência:** manter o item em estado pendente até a validação do vinho ser concluída.

#### Crescimento de volume de alertas aumentar custo operacional do processamento diário

- **Probabilidade:** baixa
- **Impacto:** médio
- **Mitigação:**
    - Indexar os campos de busca do scheduler.
    - Filtrar apenas alertas elegíveis e ainda não enviados.
    - Monitorar profundidade da fila e tempo de execução do job.
- **Plano de contingência:** fragmentar a execução do job por janelas menores ou por faixa de usuários.

---

### Critérios de aceitação

Checklist objetivo que define se a feature está pronta.

- Um usuário consegue criar, editar e excluir um item de adega com quantidade válida e vinho existente.
- Itens com safra e tempo de guarda válidos geram cálculo persistido de ponto ideal de consumo.
- O scheduler diário publica `adega.alerta.consumo` apenas uma vez por alerta elegível.
- O svc-notificacao recebe o evento e consegue processar o envio conforme o canal preferido do usuário.
- Um item da wishlist pode ser convertido em item de adega reaproveitando os dados já cadastrados.
- O inventário completo da adega pode ser exportado em CSV e em PDF sem alterar os dados de origem.
- O serviço expõe logs, métricas e tracing suficientes para rastrear um item até a notificação emitida.

---

### Testes e validação

Tipos de teste obrigatórios

- Testes unitários para regras de cálculo de ponto ideal de consumo e validação de item de adega.
- Testes de integração com PostgreSQL e RabbitMQ para persistência e publicação de eventos.
- Testes de contrato para integração com BFF e validação de payload do evento `adega.alerta.consumo`.
- Testes de reprocessamento e idempotência para evitar alertas duplicados.
- Testes de integração para conversão de wishlist em adega e exportação de inventário.

Estratégia de validação

- TDD para o cálculo de alerta e regras de negócio críticas, validação automática com Testcontainers, e QA guiado para os fluxos de conversão de wishlist, painel da adega e exportação.