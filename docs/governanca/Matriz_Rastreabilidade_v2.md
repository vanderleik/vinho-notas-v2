# Matriz de Rastreabilidade v2

Legenda de status:
- OK: rastreabilidade completa no baseline atual.
- AJUSTAR: existe mapeamento parcial ou inconsistente.

| FR | Descricao resumida | UC | AD | Jornada | Componente tecnico principal | Status |
|---|---|---|---|---|---|---|
| FR06 | Perfis Enofilo/Sommelier/Parceiro | UC02 | AD-01 | Ricardo, Andre | svc-autenticacao, svc-perfil | OK |
| FR10 | Editar/excluir vinho com aviso de impacto | UC05 | AD-02 | Ricardo, Andre | svc-vinho | OK |
| FR12 | Wishlist de vinhos | UC07 | AD-02 | Marina, Camila | svc-vinho | OK |
| FR13 | Exportar lista de vinhos CSV/PDF | UC16 | AD-07 | Ricardo, Andre | svc-vinho, svc-analytics | OK |
| FR16 | Gestao de avaliacoes (listar/editar/excluir) | UC08 | AD-03 | Marina, Camila | svc-avaliacao | OK |
| FR21 | Gestao de fichas (buscar/editar/excluir/exportar) | UC10 | AD-04 | Ricardo, Andre | svc-degustacao | OK |
| FR41 | Mover wishlist para adega | UC07 | AD-02 | Marina, Camila | svc-vinho, svc-adega | OK |
| FR42 | Marcar favorito do trimestre | UC14 | AD-06 | Ricardo | svc-analytics, svc-perfil | OK |

## Regras de manutencao
- Toda mudanca em FR deve atualizar esta matriz no mesmo PR.
- Toda mudanca em UC ou AD deve validar impacto em FR e jornadas.
- Itens AJUSTAR devem ser convertidos para OK antes do freeze de release.
