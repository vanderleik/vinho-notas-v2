# Checklist de Coerência Documental por PR

Use este checklist em toda alteração de documentação de produto, requisitos, arquitetura, diagramas e jornadas.

## 1. Escopo e produto
- O escopo in/out permanece consistente entre PRD e Requisitos.
- Não há introdução de e-commerce ou rede social completa na v2.
- O perfil Parceiro, quando citado, não inclui compra e venda dentro da plataforma.

## 2. Contratos de integracao
- Toda referência de rota externa segue o padrão /api/v1/*.
- Frontend consome apenas contratos do BFF.
- Nenhum artefato descreve consumo direto de svc-* pelo frontend.

## 3. Rastreabilidade funcional
- Todo FR alterado possui mapeamento para UC e AD.
- Toda jornada alterada referencia FR existente.
- Não existem UCs mapeados para FR incorreto (ex.: FR13 fora de exportação).

## 4. Nomenclatura e glossario
- Uso consistente de Sommelier (perfil humano) e Sommelier Virtual (IA).
- Atores canonicamente nomeados: Enófilo, Sommelier, Parceiro.
- Termos de exportação estão separados: lista de vinhos, ficha de degustação, inventário da adega.

## 5. Diagramas
- Diagramas de casos de uso e atividades estão alinhados aos FR vigentes.
- Transições condicionais não possuem ambiguidades, especialmente nos canais de notificação.
- A cobertura de requisitos nos resumos dos diagramas foi atualizada.

## 6. Gate de aprovacao
- Revisão cruzada mínima concluída: PRD x Requisitos, Requisitos x HLD, Requisitos x Diagramas.
- Pendências de coesão registradas com severidade e responsável.
- Changelog do documento atualizado com data e objetivo da alteração.
