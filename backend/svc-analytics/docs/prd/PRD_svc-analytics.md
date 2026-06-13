### PRD: Vinho Notas svc-analytics

Versão: 1.0

Data: 2026-06-12

Responsável: Vanderlei Kleinschmidt

---

### Resumo

O svc-analytics é o serviço de leitura agregada da plataforma Vinho Notas. Ele consolida eventos de domínio vindos dos demais microsserviços, mantém snapshots de dashboard e fornece dados prontos para consumo pelo BFF e pelo frontend, reduzindo a necessidade de consultas em tempo real a múltiplas fontes.

A proposta é dar suporte a uma experiência de dashboard rápida, resiliente e observável, com leitura por cache e reprocessamento operacional quando houver inconsistências. O serviço também sustenta módulos de insights e recomendações assíncronas, sempre sem interferir nos fluxos transacionais dos serviços donos dos dados.

---

### Contexto e problema

Público-alvo

- Usuários da plataforma que acompanham seu acervo, avaliações e degustações pelo dashboard.
- Enófilos amadores que querem uma visão rápida do que registraram sem navegar por vários módulos.
- Usuários avançados que acompanham ranking, gasto e indicadores históricos com frequência.

Cenários de uso chave

- Consultar um resumo consolidado das últimas atividades e indicadores pessoais.
- Atualizar automaticamente o dashboard quando eventos de vinho, avaliação, degustação ou adega forem registrados.
- Reprocessar snapshots quando houver inconsistência, falha de consumo ou necessidade de reconstrução.
- Gerar insumos agregados para recomendações assíncronas de IA.

Onde essa feature será implantada

- Backend do ecossistema Vinho Notas, como microsserviço Java 21 com Spring Boot 3.3.x, PostgreSQL, RabbitMQ e Redis, consumido pelo BFF Spring Cloud Gateway e pelo MFE-dashboard.

Problemas priorizados

- O dashboard precisa agregar dados de múltiplos serviços sem consultar cada fonte em tempo real, o que aumenta latência e fragilidade operacional. Impacto: alto, Prioridade: alta.
- Falhas temporárias em serviços produtores não podem impedir a visualização de indicadores já conhecidos. Impacto: alto, Prioridade: alta.
- A plataforma precisa de um modelo de leitura preparado para reprocessamento e reconciliação sem perda de eventos. Impacto: alto, Prioridade: média.
- O serviço precisa manter um caminho claro para gerar sugestões assíncronas de IA com base nos dados consolidados. Impacto: médio, Prioridade: média.

---

### Objetivos e métricas

| Objetivo | Métrica | Meta |
| --- | --- | --- |
| Entregar leitura rápida do dashboard | Latência p95 da API de snapshot no BFF | Menor que 150 ms |
| Manter agregados consistentes por evento | Percentual de eventos relevantes processados com sucesso após publicação | 100% via outbox, com retry até 3 tentativas e DLQ para falhas persistentes |
| Sustentar recuperação operacional | Execução de reconstrução de snapshot sem interromper leituras ativas | 0 interrupções nas consultas durante o reprocessamento |
| Preservar atualidade perceptível do dashboard | Idade máxima do snapshot em cache | Até 1 hora, conforme TTL definido |

---

### Escopo

Incluso

- Consumo de eventos de domínio dos serviços produtores da plataforma.
- Consolidação de métricas, resumo do dashboard, ranking e gasto em um read model próprio.
- Exposição de API de leitura para o BFF.
- Cache de snapshot em Redis para consultas repetidas.
- Reprocessamento e reconstrução de snapshots com base em histórico persistido.
- Geração assíncrona de solicitações de recomendação para o serviço de IA.

Fora de escopo

- Escrita transacional de domínio dos serviços produtores.
- Gestão de autenticação, login, perfil ou preferências de usuário.
- Interface visual própria para o usuário final.
- Motor de recomendação em si, que pertence ao svc-ia.
- Funcionalidades de e-commerce, pagamento ou compra de vinhos.

---

### Requisitos funcionais

#### FR01 Consolidação de eventos e snapshots

O serviço deve consumir eventos de domínio relevantes e atualizar o read model do dashboard com dados consolidados de vinhos, avaliações, degustações, adega e progressão do usuário.

**Fluxo principal**

- O serviço consome eventos publicados em RabbitMQ pelos serviços de domínio.
- Cada evento é validado, identificado e processado de forma idempotente.
- O read model em PostgreSQL é atualizado com os novos agregados.
- O snapshot consolidado é recalculado e fica pronto para leitura.
- O evento processado é marcado como concluído para evitar duplicidade.

**Fluxos alternativos e exceções**

- Se o mesmo evento chegar novamente, o consumidor deve ignorá-lo sem duplicar totais.
- Se o processamento falhar de forma transitória, o evento deve ser reenfileirado para nova tentativa.
- Se a falha persistir após as tentativas configuradas, o evento vai para DLQ.

**Erros previstos**

- Evento com schema inválido ou incompleto.
- Consumidor indisponível no momento da entrega.
- Falha de persistência no banco do serviço.

**Prioridade:** alta

**Consome**

- Eventos de domínio de vinho, avaliação, degustação, adega e gamificação emitidos pelos serviços produtores.

**Fornece**

- Snapshot consolidado do usuário, com totais de acervo, avaliações, degustações, itens de adega, ranking e gasto, usado por F02 e F04.

**Capacidades**

- Processar eventos com retry automático até 3 tentativas antes de enviar para DLQ.
- Garantir idempotência por chave de evento e identificador de correlação.
- Atualizar snapshots sem depender de chamadas síncronas aos serviços produtores.
- Manter trilha de processamento por evento para auditoria operacional.

**Experiência**

- O consumidor opera em background e não exige ação do usuário.
- Quando um evento entra na fila, o serviço deve registrar início, sucesso ou falha com traceId.
- Em falha transitória, a plataforma deve manter o snapshot anterior disponível para leitura.
- Em falha persistente, o evento deve ficar observável na DLQ para reprocessamento posterior.

**Erro Handling**

- Se o schema do evento não corresponder ao contrato esperado, o evento deve ser rejeitado e enviado para DLQ com motivo registrado.
- Se o banco estiver indisponível, o serviço deve preservar o evento na fila até a próxima tentativa e não descartar dados.
- Se for detectada duplicidade de mensagem, o evento deve ser ignorado sem alterar totais nem gerar erro ao fluxo principal.
- Se a fila principal ficar indisponível, o serviço deve manter a operação dos snapshots já persistidos e sinalizar degradação nos logs e métricas.

---

#### FR02 Consulta de snapshot do dashboard

O serviço deve expor uma API de leitura para que o BFF obtenha um snapshot consolidado do dashboard em uma única chamada.

**Fluxo principal**

- O BFF solicita o snapshot do usuário ao svc-analytics.
- O serviço localiza o snapshot consolidado mais recente.
- Se houver cache válido, a resposta vem do Redis.
- Se o cache não estiver disponível, a resposta é montada a partir do read model em PostgreSQL.
- O serviço devolve ao BFF os dados prontos para a renderização do dashboard.

**Fluxos alternativos e exceções**

- Se o cache estiver expirado, o serviço recarrega o snapshot do banco e repovoa o Redis.
- Se houver indisponibilidade do Redis, a leitura deve seguir pelo PostgreSQL sem bloquear o dashboard.
- Se o usuário não tiver dados consolidados, a resposta deve retornar um snapshot vazio, mas válido.

**Erros previstos**

- Snapshot inexistente para o usuário solicitado.
- Indisponibilidade temporária do Redis.
- Timeout de leitura acima do limite da rota.

**Prioridade:** alta

**Consome**

- Snapshot consolidado e indicadores agregados produzidos por F01.

**Capacidades**

- Atender leituras com latência baixa para o BFF.
- Usar cache Redis com TTL de 1 hora para consultas repetidas.
- Retornar payload consistente com os dados do dashboard, incluindo resumo, ranking e gasto.
- Preservar compatibilidade com rotas versionadas do BFF.

**Experiência**

- A chamada do BFF deve ser servida em uma única ida ao svc-analytics.
- Quando o cache estiver quente, a resposta deve ser entregue sem recomputar agregados.
- Quando o cache expirar, a recomputação deve ser transparente para o consumidor.
- O serviço deve continuar respondendo com a última visão consistente conhecida se ocorrer falha temporária no cache.

**Prioridade do Core Scope:** alta

**Full Scope additions**

- Cache de snapshot em Redis com TTL de 1 hora.
- Atualização do cache logo após a consolidação de novos eventos.
- Estratégia de fallback para leitura direta do PostgreSQL quando o Redis estiver indisponível.

---

#### FR03 Reprocessamento e reconstrução de snapshots

O serviço deve permitir reconstruir snapshots e reprocessar eventos quando houver inconsistência, necessidade operacional ou limpeza de backlog na DLQ.

**Fluxo principal**

- A rotina de reconstrução é acionada por perfil operacional ou comando de execução específico.
- O serviço lê o histórico persistido e os eventos pendentes para reprocessamento.
- Os agregados são recalculados a partir da fonte persistida.
- O snapshot resultante substitui a versão anterior de forma controlada.
- Ao final, o serviço registra o resultado da reconstrução e o estado final da DLQ.

**Fluxos alternativos e exceções**

- Se houver evento duplicado durante o reprocessamento, ele deve ser ignorado.
- Se o processo for interrompido, a execução deve poder ser retomada sem corromper o snapshot.
- Se a DLQ ainda contiver mensagens inválidas, elas devem permanecer separadas até correção manual.

**Erros previstos**

- Histórico insuficiente para reconstrução completa.
- Falha de leitura em lote durante o reprocessamento.
- Divergência entre snapshot reconstruído e estado persistido anterior.

**Prioridade:** média

**Consome**

- Histórico de eventos persistidos, checkpoints de processamento e estado atual dos snapshots produzidos por F01.

**Capacidades**

- Reexecutar consolidações sem duplicar totais.
- Reconstruir snapshots a partir do histórico armazenado.
- Processar mensagens reencaminhadas da DLQ de forma controlada.
- Preservar o snapshot anterior até que a reconstrução seja concluída com sucesso.

**Experiência**

- A operação deve ser feita fora do caminho crítico das leituras normais.
- O serviço deve registrar início, avanço e término da reconstrução.
- Durante a reconstrução, as consultas continuam atendendo a última versão consistente disponível.
- Quando o processo concluir, a próxima leitura já deve refletir o novo snapshot.

**Erro Handling**

- Se o reprocessamento falhar no meio do lote, o serviço deve manter a versão anterior do snapshot e registrar o ponto de interrupção.
- Se a fonte histórica estiver incompleta, a execução deve ser sinalizada como parcial e não publicar um snapshot inconsistente.
- Se houver divergência entre dados reconstruídos e persistidos, a rotina deve bloquear a substituição automática e exigir revisão operacional.
- Se a DLQ continuar com mensagens inválidas após a tentativa de replay, elas devem permanecer isoladas para tratamento manual.

---

#### FR04 Solicitação assíncrona de recomendações para IA

O serviço deve emitir solicitações assíncronas para o svc-ia com base em dados consolidados e sinais agregados do usuário.

**Fluxo principal**

- O serviço identifica que há base suficiente para uma nova recomendação.
- A solicitação é montada com os dados agregados relevantes.
- O evento `ia.recomendacao.solicitada` é publicado no RabbitMQ.
- O svc-ia consome a solicitação e gera recomendações em background.
- O histórico do envio é mantido para observabilidade e rastreio.

**Fluxos alternativos e exceções**

- Se já houver solicitação recente para o mesmo usuário, o serviço deve evitar duplicidade.
- Se o svc-ia estiver indisponível, o evento deve permanecer durável na fila.
- Se a publicação falhar, a operação deve ser reprocessada sem perder o contexto consolidado.

**Erros previstos**

- Contexto insuficiente para gerar recomendação útil.
- Falha de publicação no exchange principal.
- Backlog excessivo na fila de IA.

**Prioridade:** média

**Consome**

- Dados consolidados de comportamento, leitura e preferências agregadas produzidos por F01.

**Capacidades**

- Publicar solicitações assíncronas sem bloquear a leitura do dashboard.
- Evitar tempestade de eventos repetidos para o mesmo usuário.
- Preservar rastreabilidade entre snapshot consolidado e solicitação enviada à IA.
- Suportar retry e DLQ quando o consumidor de IA estiver indisponível.

**Experiência**

- A emissão da solicitação deve ocorrer em background.
- O usuário não deve perceber atraso no carregamento do dashboard por causa da recomendação.
- Quando a recomendação for enviada, o serviço deve registrar um identificador de correlação.
- Se a IA estiver fora, a experiência principal do dashboard continua disponível.

**Erro Handling**

- Se o evento de recomendação não puder ser publicado, ele deve ser reprocessado sem impactar a leitura do snapshot.
- Se houver duplicidade de solicitação, a nova publicação deve ser descartada com registro de idempotência.
- Se a fila de IA ficar indisponível, o serviço deve manter a mensagem durável até o consumo ocorrer.
- Se o contexto agregado estiver incompleto, a solicitação deve ser suspensa e registrada como pendente.

---

### Requisitos não funcionais

Performance

- p95 menor que 150 ms para a leitura do snapshot do dashboard via BFF.
- Processamento assíncrono deve manter o caminho síncrono de leitura independente de recomputações pesadas.
- Consultas repetidas devem ser atendidas por Redis sempre que o cache estiver válido.

Disponibilidade

- 99.9 por cento de disponibilidade mensal para a rota externa de leitura do dashboard.
- 99.5 por cento de disponibilidade para rotinas operacionais de reprocessamento.
- A indisponibilidade de um produtor não pode derrubar a leitura já consolidada do dashboard.

Segurança e controle de acesso

- O serviço recebe identidade validada pelo BFF e não expõe credenciais do usuário final.
- Somente o fluxo autorizado pelo gateway pode consultar dados agregados.
- Logs e métricas não devem expor PII desnecessária nem segredos.

Observabilidade

- Logs estruturados em JSON com traceId, spanId e correlation id.
- Métricas de taxa de erro por fila, tempo de consumo e idade do snapshot.
- Tracing distribuído ponta a ponta entre BFF, consumidores e reprocessamento.
- Alertas para crescimento de DLQ, atraso de fila e snapshot antigo demais.

Confiabilidade e integridade de dados

- Eventos de entrada devem seguir processamento idempotente.
- Publicação e consumo assíncronos devem usar fila durável com retry e DLQ.
- Reprocessamento não pode duplicar totais nem sobrescrever snapshot com estado parcial.
- O cache nunca deve ser a fonte de verdade.

Compatibilidade e portabilidade

- APIs REST JSON versionadas em `/api/v1`.
- Serviço empacotado em container OCI.
- Execução compatível com Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ 3.13 e Redis 7.2.

Compliance

- Dados agregados devem respeitar o princípio de minimização da LGPD.
- Registros operacionais devem suportar auditoria e reconciliação.
- Logs não devem reter informação pessoal desnecessária.

Acessibilidade no frontend consumidor

- Não se aplica diretamente ao serviço; a responsabilidade de acessibilidade fica no MFE-dashboard que consome os dados agregados.
- O contrato da API deve manter campos claros e estáveis para permitir renderização acessível no frontend.

---

### Arquitetura e abordagem

Abordagem

- Serviço de leitura agregado orientado a eventos, com PostgreSQL como read model principal, RabbitMQ como transporte assíncrono e Redis como cache de snapshot.
- O svc-analytics não consulta os serviços produtores em tempo real para compor o dashboard.
- O BFF consome apenas o snapshot consolidado já preparado pelo serviço.

Componentes

- Consumidor RabbitMQ para eventos de domínio.
- Persistência PostgreSQL para read model e histórico operacional.
- Redis para cache de snapshot com TTL de 1 hora.
- API REST interna para leitura pelo BFF.
- Rotina de reprocessamento e reconstrução sob perfil operacional.
- Pipeline de observabilidade com logs, métricas e tracing.

Integrações

- svc-vinho, svc-avaliacao, svc-degustacao, svc-adega e svc-gamificacao como produtores dos eventos de entrada.
- BFF como consumidor da API de snapshot.
- svc-ia como consumidor das solicitações assíncronas de recomendação.
- RabbitMQ como barramento de eventos com fila durável e DLQ.
- Redis como cache de snapshot e apoio à leitura rápida.

### Decisões e trade-offs

#### Decisão: Usar read model próprio para analytics

- **Justificativa:** reduz latência de consulta e evita dependência em tempo real de múltiplos serviços produtores.
- **Trade-off:** introduz consistência eventual e exige reprocessamento operacional em caso de divergência.

#### Decisão: Usar RabbitMQ com consumo idempotente e DLQ

- **Justificativa:** garante entrega durável e tratamento explícito de falhas de processamento.
- **Trade-off:** aumenta a complexidade operacional de fila, retry e monitoramento.

#### Decisão: Usar Redis como cache de snapshot

- **Justificativa:** melhora a experiência de leitura repetida do dashboard e reduz carga no banco.
- **Trade-off:** pode servir dados ligeiramente desatualizados até a próxima consolidação.

#### Decisão: Publicar solicitações assíncronas para o svc-ia

- **Justificativa:** mantém o dashboard desacoplado da geração de recomendações.
- **Trade-off:** a recomendação deixa de ser instantânea e passa a depender de processamento em background.

---

### Dependências

#### technical: RabbitMQ, DLQ e contrato de eventos

O serviço depende da disponibilidade do barramento RabbitMQ, das filas duráveis e da estabilidade dos contratos de evento publicados pelos serviços produtores para consumir vinho, avaliação, degustação, adega e gamificação com segurança.

#### technical: PostgreSQL e Redis

O serviço depende de um PostgreSQL dedicado para o read model e de Redis para o cache de snapshot. Sem esses dois componentes, a leitura agregada perde durabilidade ou desempenho.

#### organizational: Estabilidade do BFF e do contrato `/api/v1`

O time do BFF precisa manter estável o contrato de consumo do snapshot para que o dashboard não quebre quando o serviço evoluir.

#### external: Serviços produtores do domínio

svc-vinho, svc-avaliacao, svc-degustacao, svc-adega e svc-gamificacao precisam publicar eventos confiáveis e versionados para alimentar o snapshot e as solicitações de IA.

---

### Riscos e mitigação

#### Consistência eventual visível no dashboard

- **Probabilidade:** média
- **Impacto:** alto
- **Mitigação:**
  - consumo idempotente com persistência imediata do read model
  - cache com TTL curto e atualização contínua a partir de eventos
- **Plano de contingência:** manter a última versão consistente do snapshot até o reprocessamento normalizar os dados.

#### Crescimento de backlog ou DLQ

- **Probabilidade:** média
- **Impacto:** alto
- **Mitigação:**
  - retry limitado com DLQ monitorada
  - alertas operacionais para atraso de fila e mensagens presas
- **Plano de contingência:** executar replay controlado e, se necessário, reconstrução completa do snapshot.

#### Deriva de contrato entre produtores e analytics

- **Probabilidade:** média
- **Impacto:** alto
- **Mitigação:**
  - versionamento dos eventos
  - testes de contrato entre os serviços
- **Plano de contingência:** congelar a versão do consumidor afetado e reprocessar após ajuste do esquema.

#### Cache desatualizado ou indisponível

- **Probabilidade:** média
- **Impacto:** médio
- **Mitigação:**
  - fallback para PostgreSQL
  - repopulação automática após leitura bem-sucedida
- **Plano de contingência:** desabilitar o uso do Redis temporariamente sem interromper a leitura do dashboard.

#### Solicitação de recomendação em excesso

- **Probabilidade:** baixa
- **Impacto:** médio
- **Mitigação:**
  - controle de frequência por usuário
  - idempotência na emissão do evento de IA
- **Plano de contingência:** suspender temporariamente a emissão de novos gatilhos e manter apenas a leitura do dashboard.

---

### Critérios de aceitação

Checklist objetivo que define se a feature está pronta.

- O serviço consome eventos publicados pelos serviços produtores e atualiza o read model sem duplicar totais.
- O snapshot do dashboard fica disponível para o BFF em uma única chamada e responde com p95 menor que 150 ms.
- A leitura continua funcionando quando o Redis estiver indisponível, usando o PostgreSQL como fallback.
- Mensagens com falha persistente vão para DLQ depois de até 3 tentativas.
- O reprocessamento reconstrói o snapshot sem interromper as consultas ativas.
- A emissão de solicitação para o svc-ia ocorre de forma assíncrona e rastreável.

---

### Testes e validação

Tipos de teste obrigatórios

- Testes unitários para regras de idempotência, agregação e cálculo de snapshot.
- Testes de integração com PostgreSQL e RabbitMQ reais.
- Testes de contrato da API de leitura consumida pelo BFF.
- Testes de reprocessamento e replay de DLQ.
- Testes de observabilidade para validar logs, métricas e tracing.
- Testes de carga básicos para validar a meta de latência do dashboard.

Estratégia de validação

- TDD para as regras de consolidação e idempotência.
- Validação automatizada com Testcontainers para banco e filas.
- QA guiado para cenários de reconstrução e fallback de cache.
- Validação exploratória com foco em consistência do dashboard e recuperação operacional.
