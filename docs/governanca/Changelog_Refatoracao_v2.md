# Changelog de Refatoracao Documental v2

## Data
2026-05-24

## Objetivo
Consolidar a coesão entre PRD, requisitos, HLD, arquiteturas, diagramas, jornadas e governança.

## Mudancas aplicadas

### Produto e requisitos
- Padronizacao de APIs externas para `/api/v1/*`.
- Atualização da descrição do perfil Parceiro para manter o escopo da v2 sem compra e venda dentro da plataforma.
- Inclusão dos requisitos FR41 e FR42 para fechar lacunas de rastreabilidade.
- Revisão do mapeamento UC/FR para exportação, wishlist, dashboard e gestão documental.

### Arquitetura
- Consolidação do frontend oficial como Angular 19 com Native Federation.
- Alinhamento da fronteira de integração: o frontend consome apenas o BFF.
- Formalização da publicação de eventos de domínio via Outbox pelos serviços donos.
- Padronização de rotas e exemplos para `/api/v1/*` em backend e frontend.

### Diagramas e jornadas
- Inclusão de fluxo de gerenciamento de vinho com aviso de exclusão.
- Inclusão de fluxo de gestão de avaliações.
- Inclusão de fluxo de gestão de fichas de degustação.
- Inclusão da conversão de wishlist para adega.
- Inclusão do favorito do trimestre no dashboard e na jornada.
- Ajuste da transição ambígua no fluxo de notificações para o canal Ambos.

### Governanca
- Criação da matriz de rastreabilidade v2.
- Criação do checklist de coerência documental por PR.
- Criação das ADRs:
  - 0001-api-versioning-bff
  - 0002-domain-events-outbox
  - 0003-frontend-bff-native-federation

## Estado atual
- Rastreabilidade principal fechada.
- Documentos de arquitetura e produto alinhados ao contrato `/api/v1/*`.
- Fluxos e jornadas atualizados para os novos requisitos.

## Proximo foco sugerido
- Revisão final de acabamento textual e visual dos diagramas, se desejado.
