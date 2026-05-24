# Inventário de Projetos e Tecnologias - Vinho Notas v2

## Objetivo
Listar, de forma canônica, os projetos que devem ser criados para a v2 da plataforma, separando frontend, backend e bibliotecas compartilhadas, com suas respectivas tecnologias principais.

## Fontes de verdade
- [docs/PRD_VinhoNotas_v2.md](../PRD_VinhoNotas_v2.md)
- [docs/VinhoNotas_HLD_v2.md](../VinhoNotas_HLD_v2.md)
- [docs/arquitetura/VinhoNotas_ArquiteturaFrontend.md](../arquitetura/VinhoNotas_ArquiteturaFrontend.md)
- [docs/arquitetura/Arquitetura_de_Backend.md](../arquitetura/Arquitetura_de_Backend.md)
- [docs/adrs/0001-api-versioning-bff.md](../adrs/0001-api-versioning-bff.md)
- [docs/adrs/0002-domain-events-outbox.md](../adrs/0002-domain-events-outbox.md)
- [docs/adrs/0003-frontend-bff-native-federation.md](../adrs/0003-frontend-bff-native-federation.md)

## Escopo
Este inventário cobre apenas projetos que precisam ser criados como aplicações ou bibliotecas do workspace.

Infraestrutura compartilhada como RabbitMQ, PostgreSQL e Redis aparece apenas como dependência técnica, não como projeto principal de criação.

## Resumo executivo
- Frontend: 1 Shell, 6 MFEs e 4 bibliotecas compartilhadas.
- Backend: 1 BFF/API Gateway e 10 microsserviços.
- Infraestrutura de apoio: RabbitMQ, PostgreSQL e Redis.

## Frontend

### Shell e MFEs

| Projeto | Tipo | Responsabilidade | Tecnologias principais | Observações |
| --- | --- | --- | --- | --- |
| shell | Aplicação host | Roteamento de topo, carregamento lazy dos remotes, autenticação de sessão e registro do Service Worker | Angular 19, TypeScript 5.5+, Native Federation, Angular Router, Angular Signals, Signal Store, Angular HttpClient, @angular/pwa, SCSS, CSS Custom Properties, Jest, Cypress, ESLint, Prettier | É o host da PWA e único ponto de entrada do frontend |
| mfe-autenticacao | Aplicação remote | Login, cadastro, recuperação de senha e perfil de acesso | Angular 19, TypeScript 5.5+, Native Federation, Reactive Forms, Angular HttpClient, shared-auth, shared-models, Jest, Cypress | Consome apenas o BFF |
| mfe-vinhos | Aplicação remote | Cadastro, consulta, scan de rótulo, wishlist e adega visual | Angular 19, TypeScript 5.5+, Native Federation, Angular HttpClient, Reactive Forms, shared-models, shared-state, Jest, Cypress | Consome apenas o BFF |
| mfe-degustacao | Aplicação remote | Avaliação rápida, ficha formal, degustação comparativa e exportação | Angular 19, TypeScript 5.5+, Native Federation, Angular HttpClient, Reactive Forms, shared-models, shared-state, Jest, Cypress | Consome apenas o BFF |
| mfe-sommelier | Aplicação remote | Chat com IA, harmonização e recomendações | Angular 19, TypeScript 5.5+, Native Federation, Angular HttpClient, shared-models, shared-state, Jest, Cypress | Consome apenas o BFF |
| mfe-dashboard | Aplicação remote | Resumo, gráficos, ranking e indicadores do usuário | Angular 19, TypeScript 5.5+, Native Federation, Angular HttpClient, shared-models, shared-state, Jest, Cypress | Consome apenas o BFF |
| mfe-perfil | Aplicação remote | Dados pessoais, preferências, notificações, conquistas e nível | Angular 19, TypeScript 5.5+, Native Federation, Angular HttpClient, Reactive Forms, shared-models, shared-state, Jest, Cypress | Consome apenas o BFF |

### Bibliotecas compartilhadas

| Projeto | Tipo | Responsabilidade | Tecnologias principais | Observações |
| --- | --- | --- | --- | --- |
| shared-ui | Biblioteca | Design system, componentes reutilizáveis e tokens visuais | Angular Material 3, SCSS, CSS Custom Properties, TypeScript | Fonte de componentes e tokens visuais para todos os MFEs |
| shared-auth | Biblioteca | Autenticação transversal, guardas e interceptores JWT | TypeScript, Angular HttpClient, AuthGuard, JWT interceptor | Centraliza contratos de sessão e proteção de rotas |
| shared-models | Biblioteca | Modelos e interfaces TypeScript de domínio | TypeScript | Mantém tipos de contrato compartilhados entre MFEs |
| shared-state | Biblioteca | Estado global compartilhado entre MFEs | Angular Signals, @ngrx/signals, TypeScript | Substitui NgRx clássico por Signal Store |

## Backend

### BFF e API Gateway

| Projeto | Tipo | Responsabilidade | Tecnologias principais | Observações |
| --- | --- | --- | --- | --- |
| bff | Aplicação backend | Validação JWT, rate limiting, circuit breaker, roteamento e agregação de respostas | Java 21, Spring Boot 3.3.x, Spring Cloud Gateway 4.1.x, Spring Security, JWT, WebFlux/Netty, Resilience4j, Redis, Micrometer, Actuator, Docker | É o único ponto de entrada da API para o frontend |

### Microsserviços síncronos

| Projeto | Tipo | Responsabilidade | Tecnologias principais | Observações |
| --- | --- | --- | --- | --- |
| svc-autenticacao | Microsserviço | Cadastro, login, OAuth, refresh token e emissão de JWT | Java 21, Spring Boot 3.3.x, PostgreSQL 16, Spring Security, JWT, Flyway, Spring Data JPA, Hibernate, OAuth Google/Apple | Publica eventos de domínio quando aplicável |
| svc-perfil | Microsserviço | Preferências de idioma, tema, pontuação e notificações | Java 21, Spring Boot 3.3.x, PostgreSQL 16, Flyway, Spring Data JPA, Hibernate | Fonte de verdade do perfil do usuário |
| svc-vinho | Microsserviço | Catálogo do acervo, consulta, scan, wishlist e base de vinhos | Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ, Outbox, Flyway, Spring Data JPA, Hibernate | Publica eventos de criação/alteração de vinho |
| svc-adega | Microsserviço | Quantidade, posição física e ponto ideal de consumo | Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ, scheduler, Spring Data JPA, Hibernate | Depende de referência a svc-vinho |
| svc-avaliacao | Microsserviço | Avaliação rápida e histórico | Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ, Outbox, Flyway, Spring Data JPA, Hibernate | Base para analytics e gamificação |
| svc-degustacao | Microsserviço | Fichas formais, etapas sensoriais e degustações comparativas | Java 21, Spring Boot 3.3.x, PostgreSQL 16, JSONB, RabbitMQ, Outbox, Flyway, Spring Data JPA, Hibernate | Suporta exportação de fichas |
| svc-ia | Microsserviço | Harmonização, chat e recomendações personalizadas | Java 21, Spring Boot 3.3.x, PostgreSQL 16, Redis, Spring AI, provedor de LLM, Spring Data JPA, Hibernate | Usa cache para contexto e respostas |

### Microsserviços assíncronos

| Projeto | Tipo | Responsabilidade | Tecnologias principais | Observações |
| --- | --- | --- | --- | --- |
| svc-gamificacao | Microsserviço | Níveis, badges e progresso do usuário | Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ, Spring Data JPA, Hibernate | Consome eventos de domínio |
| svc-notificacao | Microsserviço | Agendamento, entrega push/e-mail e histórico de envio | Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ, PWA Service Worker, e-mail provider, Spring Data JPA, Hibernate | Responsável por notificações assíncronas |
| svc-analytics | Microsserviço | Snapshot de dashboard, ranking, gasto e leitura agregada | Java 21, Spring Boot 3.3.x, PostgreSQL 16, RabbitMQ, Redis, Spring Data JPA, Hibernate | Fornece agregações para o dashboard |

## Infraestrutura de apoio

| Componente | Papel | Tecnologias | Observações |
| --- | --- | --- | --- |
| RabbitMQ | Mensageria assíncrona | RabbitMQ 3.13, topic exchange, DLQ, AMQP | Suporte para eventos de domínio via Outbox |
| PostgreSQL por serviço | Persistência isolada | PostgreSQL 16, Flyway 10.x | Um banco por microsserviço |
| Redis | Cache e estado efêmero | Redis 7.2 | Usado para rate limiting, cache de chat e snapshot de dashboard |

## Contagem final esperada
- 1 projeto Shell.
- 6 projetos de MFE.
- 4 bibliotecas compartilhadas.
- 1 projeto BFF.
- 10 microsserviços backend.

## Observações de consistência
- O frontend oficial é Angular 19 com Micro Frontends e Native Federation.
- O frontend consome apenas o BFF, sem acesso direto a serviços internos.
- As rotas externas do BFF seguem o padrão `/api/v1/*`.
- Eventos de domínio são publicados apenas pelos serviços donos da transação, via Outbox.
- Referências históricas a React devem ser tratadas como legado e não como stack alvo da v2.