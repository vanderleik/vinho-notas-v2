### PRD: Vinho Notas Plataforma PWA v2.0

Versão: 2.0

Data: 2026-05-24

Responsável: Vanderlei Kleinschmidt

---

### Resumo

Plataforma PWA para registro, avaliação, organização e descoberta de vinhos, combinando micro frontends, BFF, microsserviços Java 21 e IA generativa para harmonização e recomendações. Objetivo: substituir o MVP acoplado por uma arquitetura distribuída, entregando experiências rápidas, offline-first e com recursos de comunidade, exportação e gamificação.

---

### Contexto e problema

Público-alvo

- Enófilos amadores (Marina)
- Entusiastas avançados / futuros parceiros (Ricardo, André)
- Criadores de conteúdo e público jovem (Camila)

Cenários de uso chave

- Registrar vinhos rapidamente pelo celular (scan de rótulo)
- Avaliar e documentar degustações formais e rápidas
- Receber harmonizações e recomendações via sommelier virtual (IA)
- Gerir adega virtual com alertas de ponto ideal de consumo
- Exportar fichas em PDF/CSV e compartilhar cards visuais

Onde essa feature será implantada

- Plataforma PWA frontend (Angular 19) integrada a um BFF (Spring Cloud Gateway) e microsserviços Java 21 em arquitetura cloud-native (desenvolvimento com Docker Compose, produção em Kubernetes)

Problemas priorizados

- Substituir acoplamento do MVP por arquitetura distribuída com serviços isolados (Impacto: alto, Prioridade: alta)
- Fornecer registro rápido via scan para reduzir tempo de cadastro (Impacto: médio, Prioridade: alta)
- Entregar IA de harmonização com latência controlada e fallback (Impacto: médio, Prioridade: média)
- Disponibilizar dashboard e exportações para usuários avançados (Impacto: médio, Prioridade: média)

---

### Objetivos e métricas

| Objetivo | Métrica | Meta |
| --- | --- | --- |
| Entregar fluxo de cadastro rápido por scan | Tempo médio do fluxo de cadastro por scan | < 60 segundos (hipótese) |
| Experiência responsiva no frontend | Latência p95 das APIs do BFF | p95 < 150 ms (hipótese técnica padrão) |
| Disponibilidade das APIs externas | Uptime mensal | 99.9% para interfaces externas (hipótese) |
| Adoção inicial das features principais | % usuários ativos semanais que usam avaliação/degustação | 20% WAU (hipótese de produto) |

---

### Escopo

Incluso

- Autenticação e gestão de conta (e-mail, Google/Apple, validação 18+)
- Gestão de vinhos com cadastro manual e scan de rótulo
- Avaliação rápida e fichas de degustação formais (4 etapas)
- Sommelier virtual com chat e sugestões de harmonização (IA)
- Dashboard com gráficos e exportação (CSV/PDF)
- Adega virtual com alertas de ponto ideal de consumo
- Compartilhamento: geração de cards visuais para redes sociais
- Gamificação: níveis e badges

Fora de escopo

- Funcionalidade de e-commerce (compra/venda de vinhos)
- Rede social completa com feed público e follow em larga escala
- Integrações pagas com múltiplos provedores de pagamento na primeira entrega

---

### Requisitos funcionais

#### FR01 Autenticação e Cadastro

O sistema deve permitir cadastro com nome, e-mail, senha e validação de maioridade; suportar OAuth (Google/Apple) e recuperação de senha.

**Fluxo principal**

- Usuário abre app → escolhe cadastrar → preenche nome, e-mail, senha e data de nascimento → validação 18+ → conta criada e JWT emitido.

**Fluxos alternativos e exceções**

- Cadastro via OAuth → dados básicos importados → usuário confirma perfil.
- Erro: e-mail já cadastrado → sugestão de recuperação de senha.

**Erros previstos**

- Menor de 18 anos: bloquear cadastro.
- Falha no provedor OAuth: fallback para cadastro por e-mail.

**Prioridade:** alta

---

#### FR07 Cadastro e Gestão de Vinhos (scan)

Permitir cadastrar vinho com rótulo obrigatório; suportar scan de código/QR para pré-preenchimento.

**Fluxo principal**

- Usuário → Cadastrar vinho → Aponta câmera no código → API de scan identifica rótulo → pré-preenche campos → usuário confirma e salva.

**Fluxos alternativos e exceções**

- Scan falha: usuário preenche manualmente.
- Dados conflitantes do provedor de scan: mostrar comparativo para confirmação.

**Erros previstos**

- Rótulo não reconhecido → permitir busca textual e cadastro manual.

**Prioridade:** alta

---

#### FR14 Avaliação Rápida

Registrar nota rápida (1-5 estrelas ou 0-100 pontos) com comentário opcional.

**Fluxo principal**

- Usuário seleciona vinho → Avaliar rapidamente → escolhe escala → salva → histórico atualizado.

**Fluxos alternativos e exceções**

- Conversão de escala entre estrelas/pontos conforme preferência do perfil.

**Erros previstos**

- Falha de persistência offline: salvar localmente e sincronizar quando online.

**Prioridade:** alta

---

#### FR18 Ficha de Degustação Formal

Ficha guiada em 4 etapas (visual, olfativa, gustativa, conclusão), com listas padronizadas de termos e exportação em PDF.

**Fluxo principal**

- Iniciar ficha → selecionar vinho(s) → preencher etapas guiadas → salvar → gerar PDF opcional.

**Fluxos alternativos e exceções**

- Degustação comparativa: vincular múltiplos vinhos a mesma sessão.

**Erros previstos**

- Campos obrigatórios ausentes → validação e mensagem ao usuário.

**Prioridade:** alta

---

#### FR23 Sommelier Virtual (IA)

Oferecer harmonizações, menu completo, chat contextual e recomendações baseadas no histórico do usuário via LLM.

**Fluxo principal**

- Usuário abre chat → envia contexto (vinho/ingredientes) → backend chama provedor LLM → resposta renderizada → histórico de chat salvo em cache (Redis) e persistência parcial.

**Fluxos alternativos e exceções**

- LLM indisponível ou alta latência → usar cache de respostas ou fallback informativo.

**Erros previstos**

- Respostas inconsistentes do LLM → marcar para revisão e sinalizar confiança baixa.

**Prioridade:** média

---

#### FR28 Dashboard e Insights

Exibir resumo com últimas atividades, gráficos de preferência e ranking pessoal; suportar exportação e cache de snapshot.

**Fluxo principal**

- Usuário acessa dashboard → BFF agrega dados de múltiplos serviços em paralelo → resposta otimizada e cacheada.

**Prioridade:** média

---

#### FR33 Compartilhamento e Exportação

Gerar card visual padronizado para redes sociais e exportar fichas/inventário em PDF/CSV.

**Prioridade:** média

---

#### FR36 Notificações e Lembretes

Enviar notificações push/e-mail para alertas de ponto ideal de consumo, recomendações e conquistas; usuário configura preferências de canal.

**Prioridade:** média

---

#### FR38 Gamificação

Sistema de níveis e badges baseado em atividade; exibir progresso no perfil.

**Prioridade:** baixa-média

---

### Requisitos não funcionais

Performance

- p95 das APIs do BFF: hipótese p95 < 150 ms.

Disponibilidade

- Uptime alvo: 99.9% para interfaces externas (hipótese).

Segurança e autorização

- Autenticação JWT, refresh token seguro em cookie HTTP-only, RBAC por endpoint, criptografia de senhas (bcrypt/Argon2), conformidade LGPD.

Observabilidade

- Logs estruturados JSON, métricas p95/erros, tracing distribuído (Micrometer Tracing), dashboards Grafana e alertas.

Confiabilidade e integridade de dados

- Outbox Pattern para eventos assíncronos; consumidores idempotentes; DLQ e reprocessamento.

Compatibilidade e portabilidade

- APIs REST JSON versionadas (/api/v1); serviços empacotados em containers OCI.

Compliance

- Exportação/exclusão de dados em conformidade com LGPD; trilha de auditoria para alterações sensíveis.

Acessibilidade

- Frontend conforme WCAG 2.1 AA; suporte a leitura por tela e navegação por teclado.

---

### Arquitetura e abordagem

Abordagem

- Microsserviço isolado por domínio com BFF (Spring Cloud Gateway) e PWA frontend em Angular 19 com Micro Frontends. Comunicação síncrona via HTTPS/REST e assíncrona via RabbitMQ (Outbox Pattern).

Componentes

- Shell Angular, MFEs (autenticação, vinhos, degustação, sommelier, dashboard, perfil), BFF/API Gateway, svc-autenticacao, svc-perfil, svc-vinho, svc-adega, svc-avaliacao, svc-degustacao, svc-ia, svc-gamificacao, svc-notificacao, svc-analytics, RabbitMQ, PostgreSQL por serviço, Redis.

Integrações

- Provedores OAuth (Google, Apple), provedor LLM (OpenAI/Anthropic via Spring AI), e-mail provider, observability stack (Prometheus/Grafana), storage/backup, serviços de notificação push.

---

### Decisões e trade-offs

#### Decisão: Isolamento total de bancos por serviço

- **Justificativa:** permitir deploy e evolução independentes de cada serviço; reduzir acoplamento.
- **Trade-off:** consultas agregadas ficam mais complexas e dependem do BFF; maior overhead operacional (vários bancos).

#### Decisão: Uso de RabbitMQ + Outbox Pattern para efeitos colaterais

- **Justificativa:** garantir entrega de eventos sem perder consistência transacional.
- **Trade-off:** complexidade operacional de filas, DLQ e reprocessamento; consistência eventual em alguns fluxos.

---

### Dependências

#### external: Provedores OAuth (Google, Apple)

Integração necessária para autenticação social; equipe de infraestrutura precisa configurar chaves e permissões.

#### technical: Provedor de LLM

API de LLM para sommelier virtual; requer contrato de uso e cuidado com latência/custos.

#### organizational: Design e identidades visuais

Entrega dos assets (cards, templates PDF) pelo time de design para exportação e compartilhamento.

---

### Riscos e mitigação

#### Consistência eventual entre escrita e processamento assíncrono

- **Probabilidade:** media
- **Impacto:** alto
- **Mitigação:** Outbox Pattern; consumidores idempotentes; monitor de DLQ e reprocessamento.
- **Plano de contingência:** replay manual de eventos e reconstrução de snapshots.

#### Deriva de contratos entre Shell, BFF e MFEs

- **Probabilidade:** media
- **Impacto:** alto
- **Mitigação:** versionamento OpenAPI, testes de contrato, shared models e revisão em PR.
- **Plano de contingência:** rollback do MFE afetado e congelamento temporário do contrato.

#### Latência e custo do módulo de IA

- **Probabilidade:** media
- **Impacto:** medio
- **Mitigação:** cache Redis, circuit breaker, timeouts, fallback informativo e respostas cacheadas.
- **Plano de contingência:** degradar funcionalidades de IA mantendo UX com conteúdo estático ou cache.

---

### Critérios de aceitação

Checklist objetivo que define se a feature está pronta.

- Autenticação: cadastro e login funcionando com OAuth e validação 18+; 100% dos casos de erro tratados.
- Cadastro por scan: fluxo end-to-end executado em < 60s em 90% das execuções (hipótese).
- Avaliação rápida: criar/editar/excluir avaliações com sincronização offline comprovada.
- Ficha de degustação: exportação em PDF preservando campos obrigatórios e layout.
- Sommelier virtual: respostas contextuais com fallback configurado e logs de confiança.
- Dashboard: endpoints agregados com p95 < 150 ms em ambiente de homologação (hipótese).

---

### Testes e validação

Tipos de teste obrigatórios

- Testes unitários (backend e frontend) — cobertura backend >= 80% (JaCoCo)
- Testes de integração com Testcontainers (PostgreSQL e RabbitMQ)
- Testes de contrato (OpenAPI) entre BFF e microsserviços
- Testes end-to-end do fluxo crítico (scan → cadastro → avaliação → exportação)
- Testes de carga básicos para endpoints do BFF (latência p95)
- Segurança: análise de pen test focada em autenticação e armazenamento de tokens

Estratégia de validação

- TDD nas regras críticas (cadastro, outbox e processamento de eventos), QA manual guiado por roteiro para fluxos de degustação e exportação, validação exploratória com dados reais nas jornadas de usuário.
