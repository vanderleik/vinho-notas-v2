# ADR 0003: Frontend Angular 19 com Native Federation e consumo exclusivo via BFF

## Status
Aceita

## Contexto
Os documentos historicamente referenciavam React em alguns diagramas e mostravam dependências diretas de MFEs para serviços internos.

## Decisao
O frontend oficial da v2 é Angular 19 com Micro Frontends via Native Federation. O frontend consome apenas o BFF como ponto de entrada de API; os MFEs não chamam serviços internos diretamente.

## Consequencias
- Padroniza a stack do frontend.
- Evita acoplamento dos MFEs aos microserviços de domínio.
- Exige manter shared libraries versionadas e contratos agregados no BFF.
