# ADR 0002: Publicacao de eventos de dominio via Outbox pelos servicos donos

## Status
Aceita

## Contexto
Havia ambiguidade sobre qual componente publica eventos assíncronos, inclusive com menções indevidas ao BFF como orquestrador de eventos de negócio.

## Decisão
Somente o servico dono da transacao publica eventos de dominio, utilizando Outbox Pattern. O BFF nao publica eventos de negocio; ele apenas orquestra requisicoes sincronas e agrega respostas.

## Consequencias
- Mantem consistencia transacional local ao dominio.
- Simplifica rastreabilidade entre comando, persistencia e evento.
- Requer consumidores idempotentes, DLQ e monitoramento de reprocessamento.
