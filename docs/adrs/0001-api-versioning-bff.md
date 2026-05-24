# ADR 0001: Versionamento canônico de API no BFF

## Status
Aceita

## Contexto
A documentação do projeto continha variações entre /api e /api/v1, gerando ambiguidade sobre os contratos expostos pelo BFF.

## Decisão
Todas as rotas externas expostas ao frontend devem usar o padrão /api/v1/* como contrato canônico. Exemplos e diagramas devem seguir a mesma convenção.

## Consequências
- Facilita testes de contrato e evolucao futura sem quebra silenciosa.
- Exige atualizacao coordenada de PRD, HLD, arquitetura e diagramas sempre que novas rotas forem adicionadas.
- Permite politica clara de deprecacao e versionamento.
