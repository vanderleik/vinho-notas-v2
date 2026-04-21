# Vinho Notas — Diagramas de Casos de Uso v2.0

> **Convenções UML adotadas**
> - `👤 Ator` → ator do sistema (fora do boundary)
> - `🌐 Ator Externo` → sistema ou serviço externo
> - `( )` nós arredondados → casos de uso
> - `-->` linha sólida → associação ator ↔ caso de uso
> - `-. «include» .->` seta tracejada → comportamento **obrigatório** incluído
> - `-. «extend» .->` seta tracejada → comportamento **opcional/condicional**
> - `subgraph` → boundary do sistema

---

## UC-01 — Autenticação e Gestão de Conta

Cobre os casos de uso relacionados ao acesso ao sistema, cadastro de usuários e gerenciamento de perfil. Inclui o fluxo de autenticação social via OAuth e a validação obrigatória de maioridade.

**Requisitos:** FR01, FR02, FR03, FR04, FR05, FR06

```mermaid
flowchart LR
    usuario["👤 Usuário"]
    oauth["🌐 Provedor OAuth\n(Google / Apple)"]

    subgraph SYS["🍷 Vinho Notas — Autenticação e Conta"]
        direction TB
        uc01("UC01\nAutenticar no sistema")
        uc01a("UC01-A\nCadastrar nova conta")
        uc01b("UC01-B\nRecuperar senha")
        uc01c("UC01-C\nValidar maioridade 18+")
        uc01d("UC01-D\nAutenticar via OAuth")
        uc02("UC02\nGerenciar perfil")
        uc02a("UC02-A\nConfigurar preferências e tema")
    end

    usuario --> uc01
    usuario --> uc01a
    usuario --> uc01b
    usuario --> uc02
    oauth   --> uc01d

    uc01  -. "«extend»"  .-> uc01a
    uc01  -. "«extend»"  .-> uc01b
    uc01  -. "«extend»"  .-> uc01d
    uc01a -. "«include»" .-> uc01c
    uc02  -. "«include»" .-> uc02a

    classDef uc     fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef ucInc  fill:#EFF6FF,stroke:#4A7C8B,color:#1A0A0F
    classDef ucExt  fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef actor  fill:#F5F5F5,stroke:#2A1420,color:#1A0A0F
    classDef extAct fill:#F0F5FF,stroke:#1A3A4A,color:#1A3A4A

    class uc01,uc01a,uc01b,uc01d,uc02 uc
    class uc01c,uc02a ucInc
    class usuario actor
    class oauth extAct
```

---

## UC-02 — Gestão de Vinhos

Abrange o ciclo completo do acervo: cadastro manual ou por escaneamento de rótulo, consulta com filtros avançados, adega virtual com alertas de ponto de consumo ideal e lista de desejos.

**Requisitos:** FR07, FR08, FR09, FR10, FR11, FR12, FR13

```mermaid
flowchart LR
    enofilo["👤 Enófilo"]
    sommelier["👤 Sommelier"]

    subgraph SYS["🍷 Vinho Notas — Gestão de Vinhos"]
        direction TB
        uc03("UC03\nCadastrar vinho")
        uc04("UC04\nEscanear rótulo por câmera")
        uc03a("UC03-A\nValidar e salvar rótulo")
        uc05("UC05\nConsultar e filtrar vinhos")
        uc05a("UC05-A\nFiltros avançados\nuva · país · safra · preço")
        uc06("UC06\nGerenciar adega virtual")
        uc06a("UC06-A\nAlertar ponto de consumo")
        uc07("UC07\nGerenciar wishlist")
        uc07a("UC07-A\nMover vinho para adega")
    end

    enofilo   --> uc03
    enofilo   --> uc05
    enofilo   --> uc06
    enofilo   --> uc07
    sommelier --> uc03
    sommelier --> uc05
    sommelier --> uc06

    uc03  -. "«extend»"  .-> uc04
    uc03  -. "«include»" .-> uc03a
    uc05  -. "«include»" .-> uc05a
    uc06  -. "«include»" .-> uc06a
    uc07  -. "«extend»"  .-> uc07a

    classDef uc     fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef ucInc  fill:#EFF6FF,stroke:#4A7C8B,color:#1A0A0F
    classDef ucExt  fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef actor  fill:#F5F5F5,stroke:#2A1420,color:#1A0A0F

    class uc03,uc05,uc06,uc07 uc
    class uc03a,uc05a,uc06a ucInc
    class uc04,uc07a ucExt
    class enofilo,sommelier actor
```

---

## UC-03 — Avaliação e Degustação

Contempla os dois modos de registro sensorial: a **avaliação rápida** (informal, para consumo cotidiano) e a **ficha de degustação formal**, com guia pelas 4 etapas clássicas da análise sensorial. Inclui degustações comparativas e exportação em PDF.

**Requisitos:** FR14, FR15, FR16, FR17, FR18, FR19, FR20, FR21, FR22

```mermaid
flowchart LR
    enofilo["👤 Enófilo"]
    sommelier["👤 Sommelier"]

    subgraph SYS["🍷 Vinho Notas — Avaliação e Degustação"]
        direction TB

        subgraph AVR["Avaliação Rápida"]
            direction TB
            uc08("UC08\nFazer avaliação rápida")
            uc08a("UC08-A\nRegistrar percepções sensoriais")
            uc08b("UC08-B\nAtribuir nota · estrelas ou pontos")
        end

        subgraph DEG["Ficha de Degustação Formal"]
            direction TB
            uc09("UC09\nCriar ficha de degustação")

            subgraph ETAPAS["4 Etapas da Análise Sensorial"]
                direction TB
                uc09i("① Inspeção Visual")
                uc09o("② Análise Olfativa")
                uc09g("③ Análise Gustativa")
                uc09c("④ Conclusão e Nota Final")
            end

            uc09comp("UC09-C\nDegustação comparativa")
        end

        uc10("UC10\nGerenciar degustações")
        uc10a("UC10-A\nExportar ficha em PDF")
    end

    enofilo   --> uc08
    enofilo   --> uc09
    enofilo   --> uc10
    sommelier --> uc09
    sommelier --> uc10

    uc08  -. "«include»" .-> uc08a
    uc08  -. "«include»" .-> uc08b
    uc09  -. "«include»" .-> ETAPAS
    uc09  -. "«extend»"  .-> uc09comp
    uc10  -. "«include»" .-> uc10a

    classDef uc     fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef ucInc  fill:#EFF6FF,stroke:#4A7C8B,color:#1A0A0F
    classDef ucExt  fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef actor  fill:#F5F5F5,stroke:#2A1420,color:#1A0A0F
    classDef etapa  fill:#F0F8FF,stroke:#4A7C8B,color:#1A0A0F,font-style:italic

    class uc08,uc09,uc10 uc
    class uc08a,uc08b,uc10a ucInc
    class uc09comp ucExt
    class uc09i,uc09o,uc09g,uc09c etapa
    class enofilo,sommelier actor
```

---

## UC-04 — Sommelier Virtual / Inteligência Artificial

Representa as interações com o módulo de IA generativa (LLM). O **Sistema de IA** é tratado como ator externo, consumido pelas funcionalidades de harmonização, geração de menu, chat e recomendações personalizadas baseadas no histórico do usuário.

**Requisitos:** FR23, FR24, FR25, FR26, FR27

```mermaid
flowchart LR
    enofilo["👤 Enófilo"]
    ia["🌐 Sistema de IA / LLM\n(Spring AI · OpenAI)"]

    subgraph SYS["🍷 Vinho Notas — Sommelier Virtual / IA"]
        direction TB
        uc11("UC11\nConsultar harmonizações")
        uc11a("UC11-A\nSugerir menu completo\nentrada · prato · sobremesa")
        uc11b("UC11-B\nHarmonizar por ingredientes")
        uc12("UC12\nInteragir com sommelier virtual")
        uc12a("UC12-A\nManter histórico de chat")
        uc13("UC13\nReceber recomendações personalizadas")
        uc13a("UC13-A\nAnalisar perfil de preferências")
    end

    enofilo --> uc11
    enofilo --> uc12
    enofilo --> uc13
    ia      --> uc11
    ia      --> uc12
    ia      --> uc13

    uc11  -. "«extend»"  .-> uc11a
    uc11  -. "«extend»"  .-> uc11b
    uc12  -. "«include»" .-> uc12a
    uc13  -. "«include»" .-> uc13a

    classDef uc     fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef ucInc  fill:#EFF6FF,stroke:#4A7C8B,color:#1A0A0F
    classDef ucExt  fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef actor  fill:#F5F5F5,stroke:#2A1420,color:#1A0A0F
    classDef extAct fill:#F0F5FF,stroke:#1A3A4A,color:#1A3A4A

    class uc11,uc12,uc13 uc
    class uc12a,uc13a ucInc
    class uc11a,uc11b ucExt
    class enofilo actor
    class ia extAct
```

---

## UC-05 — Dashboard, Compartilhamento e Extras

Agrupa os módulos transversais: painel analítico com insights de preferências, compartilhamento em redes sociais com geração de cards visuais, exportação de dados, sistema de notificações push/e-mail e programa de gamificação com níveis e conquistas.

**Requisitos:** FR28–FR40

```mermaid
flowchart LR
    enofilo["👤 Enófilo"]
    push["🌐 Serviço de\nNotificação\n(Push / E-mail)"]

    subgraph SYS["🍷 Vinho Notas — Dashboard, Compartilhamento e Extras"]
        direction TB

        subgraph DASH["Dashboard e Insights"]
            direction TB
            uc14("UC14\nVisualizar dashboard e insights")
            uc14a("UC14-A\nGráficos de preferências")
            uc14b("UC14-B\nRanking pessoal de vinhos")
        end

        subgraph SHARE["Compartilhamento e Exportação"]
            direction TB
            uc15("UC15\nCompartilhar avaliação nas redes")
            uc15a("UC15-A\nGerar card visual")
            uc16("UC16\nExportar dados e fichas")
            uc16a("UC16-A\nGerar CSV / PDF")
        end

        subgraph EXTRAS["Notificações e Gamificação"]
            direction TB
            uc17("UC17\nGerenciar notificações")
            uc17a("UC17-A\nPersonalizar canais e alertas")
            uc18("UC18\nAcompanhar progresso e conquistas")
            uc18a("UC18-A\nExibir badges e níveis")
        end
    end

    enofilo --> uc14
    enofilo --> uc15
    enofilo --> uc16
    enofilo --> uc17
    enofilo --> uc18
    push    --> uc17

    uc14  -. "«include»" .-> uc14a
    uc14  -. "«include»" .-> uc14b
    uc15  -. "«include»" .-> uc15a
    uc16  -. "«include»" .-> uc16a
    uc17  -. "«extend»"  .-> uc17a
    uc18  -. "«include»" .-> uc18a

    classDef uc     fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef ucInc  fill:#EFF6FF,stroke:#4A7C8B,color:#1A0A0F
    classDef ucExt  fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef actor  fill:#F5F5F5,stroke:#2A1420,color:#1A0A0F
    classDef extAct fill:#F0F5FF,stroke:#1A3A4A,color:#1A3A4A

    class uc14,uc15,uc16,uc17,uc18 uc
    class uc14a,uc14b,uc15a,uc16a,uc18a ucInc
    class uc17a ucExt
    class enofilo actor
    class push extAct
```

---

## Resumo — Mapeamento Completo de Casos de Uso

| UC | Nome | Módulo | Atores | Requisitos |
|---|---|---|---|---|
| UC01 | Autenticar no sistema | Autenticação | Usuário, OAuth | FR01–FR04 |
| UC02 | Gerenciar perfil | Autenticação | Usuário | FR05–FR06 |
| UC03 | Cadastrar vinho | Vinhos | Enófilo, Sommelier | FR07–FR08 |
| UC04 | Escanear rótulo por câmera | Vinhos | Enófilo | FR08 |
| UC05 | Consultar e filtrar vinhos | Vinhos | Enófilo, Sommelier | FR09–FR10 |
| UC06 | Gerenciar adega virtual | Vinhos | Enófilo, Sommelier | FR11 |
| UC07 | Gerenciar wishlist | Vinhos | Enófilo | FR12–FR13 |
| UC08 | Fazer avaliação rápida | Degustação | Todos | FR14–FR17 |
| UC09 | Criar ficha de degustação | Degustação | Enófilo, Sommelier | FR18–FR22 |
| UC10 | Gerenciar degustações | Degustação | Enófilo, Sommelier | FR21–FR22 |
| UC11 | Consultar harmonizações | IA | Enófilo, IA/LLM | FR23–FR24 |
| UC12 | Interagir com sommelier virtual | IA | Enófilo, IA/LLM | FR25 |
| UC13 | Receber recomendações | IA | Enófilo, IA/LLM | FR26–FR27 |
| UC14 | Visualizar dashboard e insights | Dashboard | Enófilo | FR28–FR32 |
| UC15 | Compartilhar avaliação | Compartilhamento | Enófilo | FR33 |
| UC16 | Exportar dados e fichas | Compartilhamento | Enófilo, Sommelier | FR34–FR35 |
| UC17 | Gerenciar notificações | Extras | Todos | FR36–FR37 |
| UC18 | Acompanhar progresso | Extras | Todos | FR38–FR40 |

---

*Vinho Notas v2.0 — Elaborado com base no TCC de Vanderlei Kleinschmidt (2024)*
