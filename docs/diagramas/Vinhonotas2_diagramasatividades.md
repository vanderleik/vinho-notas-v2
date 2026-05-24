# Vinho Notas — Diagramas de Atividades v2.0
 
> **Contexto:** O TCC original (2024) apresentou um único diagrama de atividades consolidado
> cobrindo os quatro casos de uso do MVP. A versão 2.0 expande esse modelo para **8 diagramas
> individuais**, um por módulo funcional, detalhando os fluxos enriquecidos com os novos
> requisitos aprovados — OAuth, scan de rótulo, adega virtual, IA generativa, dashboard,
> compartilhamento, exportação e gamificação.
>
> **Convenções adotadas**
> - `([●])` Nó de início · `([◉])` Nó de fim
> - `[Ação]` Atividade · `{Decisão?}` Desvio condicional
> - `[[Subprocesso]]` Processo referenciado em outro diagrama
> - Setas rotuladas indicam condição da transição
> - `subgraph` delimita raias de responsabilidade (ator / sistema)
 
---
 
## AD-01 — Autenticação e Gestão de Conta
 
Cobre o fluxo completo de acesso ao sistema: primeiro acesso com cadastro e validação de
maioridade, autenticação por e-mail/senha e autenticação social via OAuth, além da recuperação
de senha. Corresponde aos requisitos **FR01–FR06**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S --> A1["Acessar o Vinho Notas"]
    A1 --> D1{"Possui conta?"}
 
    D1 -- "Sim" --> A2["Acessar tela de login"]
    D1 -- "Não" --> A10["Acessar tela de cadastro"]
 
    %% ── Fluxo de Login ──────────────────────────────
    A2 --> D2{"Como deseja\nautenticar?"}
 
    D2 -- "E-mail e senha" --> A3["Informar e-mail e senha"]
    A3 --> A4["Enviar credenciais ao servidor"]
    A4 --> D3{"Credenciais\nválidas?"}
    D3 -- "Não" --> A5["Exibir mensagem de erro"]
    A5 --> D4{"Deseja recuperar\nsenha?"}
    D4 -- "Sim" --> A6["Informar e-mail de recuperação"]
    A6 --> A7["Sistema envia link por e-mail"]
    A7 --> A8["Usuário redefine a senha"]
    A8 --> A2
    D4 -- "Não" --> A2
    D3 -- "Sim" --> A9["Gerar e armazenar JWT"]
 
    D2 -- "OAuth Google/Apple" --> B1["Redirecionar para provedor OAuth"]
    B1 --> B2["Usuário autentica no provedor"]
    B2 --> B3["Receber token de autorização"]
    B3 --> B4{"Token válido?"}
    B4 -- "Não" --> B5["Exibir erro de autenticação"]
    B5 --> A2
    B4 -- "Sim" --> B6{"Usuário já\ncadastrado?"}
    B6 -- "Não" --> B7["Criar conta via dados do OAuth"]
    B7 --> A9
    B6 -- "Sim" --> A9
 
    %% ── Fluxo de Cadastro ───────────────────────────
    A10 --> C1["Informar nome, e-mail,\nsenha e data de nascimento"]
    C1  --> C2["Sistema valida campos"]
    C2  --> D5{"Maior de\n18 anos?"}
    D5  -- "Não" --> C3["Bloquear acesso\nExibir aviso legal"]
    C3  --> E(["◉ Fim"])
    D5  -- "Sim" --> C4["Criar conta no sistema"]
    C4  --> C5["Enviar e-mail de confirmação"]
    C5  --> A9
 
    A9  --> D6["Redirecionar para o Dashboard"]
    D6  --> E
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef erro   fill:#FFF0F0,stroke:#CC4444,color:#330000
    classDef sys    fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
 
    class S start
    class E fim
    class A1,A2,A3,A4,A6,A7,A8,A9,A10,B1,B2,B3,B7,C1,C2,C4,C5,D6 acao
    class D1,D2,D3,D4,B4,B6,D5 dec
    class A5,B5,C3 erro
```
 
---
 
## AD-02 — Cadastro de Vinho e Adega Virtual
 
Detalha o fluxo de registro de um vinho no acervo pessoal, com duas entradas possíveis:
cadastro manual (formulário) ou escaneamento de rótulo pela câmera. Ao final, o usuário
pode adicionar a garrafa à adega virtual e o sistema agenda alertas de ponto de consumo ideal.
Corresponde aos requisitos **FR07–FR13, FR41**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S  --> A1["Usuário acessa o menu Vinhos"]
    A1 --> D1{"Como cadastrar?"}
    D1 -- "Gerenciar vinho existente" --> M1["Abrir lista de vinhos cadastrados"]
    M1 --> M2["Selecionar vinho para editar ou excluir"]
    M2 --> M3{"Ação desejada?"}
    M3 -- "Editar" --> M4["Alterar dados do vinho e salvar"]
    M3 -- "Excluir" --> M5{"Existem avaliações\nou degustações associadas?"}
    M5 -- "Sim" --> M6["Exibir aviso de impacto\nantes de confirmar a exclusão"]
    M5 -- "Não" --> M7["Confirmar exclusão do vinho"]
    M4 --> F1
    M6 --> F1
    M7 --> F1
 
    %% ── Via Scan ────────────────────────────────────
    D1 -- "Escanear rótulo" --> B1["Abrir câmera do dispositivo"]
    B1 --> B2["Escanear código de barras / QR code"]
    B2 --> D2{"Rótulo\nidentificado?"}
    D2 -- "Não" --> B3["Notificar: rótulo não encontrado"]
    B3 --> D1
    D2 -- "Sim" --> B4["Preencher campos automaticamente\nnome · produtor · país · uva · safra"]
    B4 --> B5["Usuário revisa e edita os dados"]
    B5 --> C1
 
    %% ── Manual ──────────────────────────────────────
    D1 -- "Cadastro manual" --> A2["Exibir formulário de cadastro"]
    A2 --> A3["Preencher rótulo (obrigatório)"]
    A3 --> A4["Preencher campos opcionais\npreço · local · data · uva · safra · etc."]
    A4 --> C1
 
    %% ── Fluxo comum ─────────────────────────────────
    C1["Validar dados do formulário"]
    C1 --> D3{"Dados\nválidos?"}
    D3 -- "Não" --> C2["Exibir erros de validação"]
    C2 --> A3
    D3 -- "Sim" --> D4{"Adicionar à\nadega virtual?"}
 
    D4 -- "Sim" --> D5["Informar quantidade de garrafas\ne posição na adega (opcional)"]
    D5 --> D6["Salvar vinho + dados da adega"]
    D4 -- "Não" --> D7["Salvar vinho no acervo"]

    D6 --> W1{"Origem do item\né Wishlist?"}
    D7 --> W1
    W1 -- "Sim" --> W2["Mover item da Wishlist\npara a adega virtual"]
    W2 --> E1
    W1 -- "Não" --> E1
 
    E1{"Safra e tempo de\nguarda informados?"}
    E1 -- "Sim" --> E2["Calcular ponto ideal de consumo"]
    E2 --> E3["Agendar alerta de ponto de consumo"]
    E3 --> F1
    E1 -- "Não" --> F1
 
    F1["Exibir confirmação de cadastro"]
    F1 --> D8{"Deseja adicionar\nà Wishlist?"}
    D8 -- "Sim" --> F2["Adicionar vinho à lista de desejos"]
    F2 --> E(["◉ Fim"])
    D8 -- "Não" --> E
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef erro   fill:#FFF0F0,stroke:#CC4444,color:#330000
    classDef sys    fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
 
    class S start
    class E fim
    class A1,A2,A3,A4,B1,B2,B4,B5,C1,D5,D6,D7,W2,E2,E3,F1,F2 acao
    class D1,D2,D3,D4,W1,E1,D8 dec
    class B3,C2 erro
```
 
---
 
## AD-03 — Avaliação Rápida de Vinho
 
Representa o fluxo da avaliação informal, pensada para o momento de consumo cotidiano —
abertura de uma garrafa em casa, jantar com amigos etc. O usuário pode atribuir nota e registrar
percepções opcionais. Ao concluir, o sistema verifica se há conquistas de gamificação a liberar.
Corresponde aos requisitos **FR14–FR17**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S  --> A1["Usuário acessa o acervo de vinhos"]
    A1 --> A2["Selecionar vinho para avaliar"]
    A2 --> D1{"Vinho possui\navaliação anterior?"}
 
    D1 -- "Sim" --> A3["Exibir histórico de avaliações\ndo rótulo selecionado"]
    A3 --> A4["Iniciar nova avaliação"]
    D1 -- "Não" --> A4
 
    A4  --> B1{"Registrar percepções\nsensoriais?"}
    B1  -- "Sim" --> B2["Registrar aspectos visuais\ncor · brilho · viscosidade"]
    B2  --> B3["Registrar aromas identificados\nfrutas · especiarias · madeira · floral"]
    B3  --> B4["Registrar sensações gustativas\nfinal de boca · acidez · corpo"]
    B4  --> C1
    B1  -- "Não" --> C1
 
    C1["Selecionar sistema de pontuação\n★ estrelas (1–5) ou pontos (0–100)"]
    C1  --> C2["Atribuir nota ao vinho"]
    C2  --> D2{"Adicionar\ncomentário livre?"}
    D2  -- "Sim" --> C3["Redigir comentário"]
    C3  --> D3
    D2  -- "Não" --> D3
 
    D3["Salvar avaliação com data e hora"]
    D3  --> D4["Sistema atualiza histórico do vinho"]
    D4 --> L1{"Deseja gerenciar\na lista de avaliações?"}
    L1 -- "Sim" --> L2["Abrir tela de avaliações"]
    L2 --> L3["Listar avaliações por data, nota ou rótulo"]
    L3 --> L4{"Editar ou excluir?"}
    L4 -- "Editar" --> L5["Alterar avaliação e salvar"]
    L4 -- "Excluir" --> L6["Confirmar exclusão da avaliação"]
    L5 --> E1
    L6 --> E1
    L1 -- "Não" --> E1
    D4  --> E1{"Sistema verifica\nconquistas de gamificação"}
    E1  -- "Nova conquista" --> E2["Exibir badge desbloqueado\nAtualizar nível do usuário"]
    E2  --> F1
    E1  -- "Sem novidade" --> F1
 
    F1{"Compartilhar\navaliação?"}
    F1  -- "Sim" --> G1[["AD-07 — Compartilhamento\ne Exportação"]]
    G1  --> E(["◉ Fim"])
    F1  -- "Não" --> E
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef sub    fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
    classDef gami   fill:#F0FFF0,stroke:#2E8B2E,color:#0A200A
 
    class S start
    class E fim
    class A1,A2,A3,A4,B2,B3,B4,C1,C2,C3,D3,D4,L2,L3,L5,L6 acao
    class D1,B1,D2,F1,E1,L1,L4 dec
    class G1 sub
    class E2 gami
```
 
---
 
## AD-04 — Ficha de Degustação Formal
 
Guia o usuário pelas **4 etapas clássicas da análise sensorial**: Inspeção Visual, Análise
Olfativa, Análise Gustativa e Conclusão. Suporta degustações comparativas com múltiplos vinhos.
O resultado pode ser exportado em PDF com identidade visual da plataforma.
Corresponde aos requisitos **FR18–FR22**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S  --> A1["Usuário acessa o menu Degustação"]
    A1 --> A2["Selecionar vinho para degustar"]
    A2 --> D1{"Tipo de\ndegustação?"}
 
    D1 -- "Comparativa" --> C1["Selecionar múltiplos vinhos\npara a sessão"]
    C1 --> A3
    D1 -- "Outros tipos\nvertical · horizontal\ntemática · às cegas" --> A3
 
    A3["Definir data, tipo e local (opcional)"]
    A3 --> A4["Iniciar ficha de degustação"]
 
    %% ── Etapa 1 ─────────────────────────────────────
    A4  --> E1["① INSPEÇÃO VISUAL\nAnalisar cor, intensidade,\nbrilho, turbidez e viscosidade"]
    E1  --> E1A["Selecionar termos na lista padronizada"]
    E1A --> E1B["Registrar observações visuais"]
 
    %% ── Etapa 2 ─────────────────────────────────────
    E1B --> E2["② ANÁLISE OLFATIVA\nIdentificar intensidade e aromas"]
    E2  --> E2A["Selecionar aromas:\nprimários · secundários · terciários"]
    E2A --> E2B["Registrar observações olfativas"]
 
    %% ── Etapa 3 ─────────────────────────────────────
    E2B --> E3["③ ANÁLISE GUSTATIVA\nAtaque, acidez, taninos, corpo,\nálcool, doçura e persistência"]
    E3  --> E3A["Selecionar termos na lista padronizada"]
    E3A --> E3B["Registrar observações gustativas"]
 
    %% ── Etapa 4 ─────────────────────────────────────
    E3B --> E4["④ CONCLUSÃO\nEscrever avaliação geral\ne potencial de guarda"]
    E4  --> E4A["Atribuir nota final ao vinho"]
 
    %% ── Comparativa ─────────────────────────────────
    E4A --> D2{"Degustação\ncomparativa?"}
    D2  -- "Sim" --> D3{"Há mais vinhos\na analisar?"}
    D3  -- "Sim" --> A2
    D3  -- "Não" --> F1["Registrar comparação geral\nentre os vinhos da sessão"]
    F1  --> G1
    D2  -- "Não" --> G1
 
    G1["Salvar ficha de degustação"]
    G1  --> H1{"Sistema verifica\nconquistas de gamificação"}
    G1 --> J1{"Deseja gerenciar\nfichas registradas?"}
    J1 -- "Sim" --> J2["Abrir tela de fichas"]
    J2 --> J3["Listar fichas com busca e filtros"]
    J3 --> J4{"Editar, excluir ou exportar?"}
    J4 -- "Editar" --> J5["Alterar ficha e salvar"]
    J4 -- "Excluir" --> J6["Confirmar exclusão da ficha"]
    J4 -- "Exportar" --> J7["Exportar ficha individual em PDF"]
    J5 --> H1
    J6 --> H1
    J7 --> H1
    J1 -- "Não" --> H1
    H1  -- "Nova conquista" --> H2["Exibir badge e atualizar nível"]
    H2  --> I1
    H1  -- "Sem novidade" --> I1
 
    I1{"Exportar ficha\nem PDF?"}
    I1  -- "Sim" --> I2[["AD-07 — Compartilhamento\ne Exportação"]]
    I2  --> E(["◉ Fim"])
    I1  -- "Não" --> E
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef etapa  fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef sub    fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
    classDef gami   fill:#F0FFF0,stroke:#2E8B2E,color:#0A200A
 
    class S start
    class E fim
    class A1,A2,A3,A4,C1,G1,J2,J3,J5,J6,J7 acao
    class E1,E1A,E1B,E2,E2A,E2B,E3,E3A,E3B,E4,E4A,F1 etapa
    class D1,D2,D3,I1,H1,J1,J4 dec
    class I2 sub
    class H2 gami
```
 
---
 
## AD-05 — Sommelier Virtual / Inteligência Artificial
 
Detalha os três modos de interação com o módulo de IA generativa: **Harmonização** (por vinho
ou por ingredientes, com geração de menu completo), **Chat** com o sommelier virtual e
**Recomendações** personalizadas baseadas no perfil de preferências do usuário.
Corresponde aos requisitos **FR23–FR27**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S  --> A1["Usuário acessa o módulo de IA"]
    A1 --> D1{"Selecionar modo"}
 
    %% ── Modo Harmonização ───────────────────────────
    D1  -- "Harmonização" --> H1{"Por vinho\nou ingredientes?"}
 
    H1  -- "Por vinho" --> H2["Selecionar vinho do acervo"]
    H2  --> H4["Compor prompt com dados do vinho\n(tipo · uva · região · safra)"]
 
    H1  -- "Por ingredientes" --> H3["Informar ingredientes\nda refeição planejada"]
    H3  --> H4
 
    H4  --> H5["Enviar requisição à IA / LLM"]
    H5  --> H6{"IA disponível?"}
    H6  -- "Não" --> H7["Exibir mensagem de serviço\nindisponível · tentar novamente"]
    H7  --> E(["◉ Fim"])
    H6  -- "Sim" --> H8["Receber sugestões de harmonização\n(alimentos e preparos)"]
    H8  --> H9["Exibir sugestões ao usuário"]
    H9  --> D2{"Gerar menu\ncompleto?"}
    D2  -- "Sim" --> H10["Solicitar à IA:\nentrada · prato principal · sobremesa"]
    H10 --> H11["Receber e exibir menu harmonizado"]
    H11 --> Z1
    D2  -- "Não" --> Z1
 
    %% ── Modo Chat ───────────────────────────────────
    D1  -- "Chat com sommelier" --> C1["Exibir histórico de mensagens\ndo usuário"]
    C1  --> C2["Usuário digita pergunta\n(temperatura · taça · aeração · região...)"]
    C2  --> C3["Sistema adiciona contexto:\nvinho selecionado · histórico do chat"]
    C3  --> C4["Enviar para IA / LLM"]
    C4  --> C5["Receber resposta do sommelier virtual"]
    C5  --> C6["Salvar conversa no histórico"]
    C6  --> D3{"Fazer nova\npergunta?"}
    D3  -- "Sim" --> C2
    D3  -- "Não" --> Z1
 
    %% ── Modo Recomendações ──────────────────────────
    D1  -- "Recomendações" --> R1["Sistema analisa histórico\nde avaliações e preferências"]
    R1  --> R2["Identificar: tipos · uvas · países\nfaixa de nota mais frequente"]
    R2  --> R3["Compor prompt com perfil do usuário"]
    R3  --> R4["Solicitar sugestões à IA"]
    R4  --> R5["Receber lista de rótulos recomendados"]
    R5  --> R6["Exibir 'Você pode gostar de...'"]
    R6  --> D4{"Adicionar sugestão\nà Wishlist?"}
    D4  -- "Sim" --> R7["Salvar vinho na lista de desejos"]
    R7  --> Z1
    D4  -- "Não" --> Z1
 
    Z1["Registrar interação com IA\npara melhoria do perfil"]
    Z1  --> E(["◉ Fim"])
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef ia     fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef erro   fill:#FFF0F0,stroke:#CC4444,color:#330000
 
    class S start
    class E fim
    class A1,H2,H3,H4,H8,H9,H10,H11,C1,C2,C3,C5,C6,R1,R2,R3,R5,R6,R7,Z1 acao
    class H5,C4,R4 ia
    class D1,H1,H6,D2,D3,D4 dec
    class H7 erro
```
 
---
 
## AD-06 — Dashboard e Insights
 
Descreve o fluxo de carregamento e navegação no painel analítico pessoal. O sistema agrega
dados do acervo, avaliações e adega do usuário para gerar gráficos, rankings e alertas.
Corresponde aos requisitos **FR28–FR32, FR42**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S  --> A1["Usuário acessa o Dashboard"]
    A1 --> A2["Sistema carrega dados do usuário\n(acervo · avaliações · adega · gastos)"]
    A2 --> D1{"Dados\ncarregados?"}
    D1 -- "Erro" --> A3["Exibir mensagem de erro\ncom opção de recarregar"]
    A3 --> E(["◉ Fim"])
    D1 -- "Sim" --> B1
 
    B1["Exibir resumo de atividades recentes\núltimas avaliações · últimas degustações"]
    B1 --> B2["Exibir nível de gamificação\ne conquistas recentes"]
 
    B2 --> C1["Renderizar gráfico:\ndistribuição por tipo de vinho"]
    C1 --> C2["Renderizar gráfico:\ndistribuição por país e uva"]
    C2 --> C3["Renderizar gráfico:\nevolução das notas ao longo do tempo"]
    C3 --> C4["Exibir ranking pessoal\n(Top 5 / Top 10 vinhos)"]
    C4 --> C4A{"Marcar favorito\ndo trimestre?"}
    C4A -- "Sim" --> C4B["Salvar vinho favorito\ncom referência ao período"]
    C4A -- "Não" --> D2
    C4B --> D2

    D2{"Usuário tem adega\ncom safra e tempo de guarda?"}
    D2 -- "Sim" --> D3["Calcular pontos de consumo ideal\npara cada vinho da adega"]
    D3 --> D4{"Há vinhos no\npico ou próximos?"}
    D4 -- "Sim" --> D5["Exibir alerta de ponto de consumo"]
    D5 --> E1
    D4 -- "Não" --> E1
    D2 -- "Não" --> E1
 
    E1{"Usuário registrou\npreços de compra?"}
    E1 -- "Sim" --> E2["Renderizar gráfico de gastos\npor período (mensal / anual)"]
    E2 --> F1
    E1 -- "Não" --> F1
 
    F1["Exibir painel completo ao usuário"]
    F1 --> D3A{"Usuário seleciona\nseção para detalhar?"}
    D3A -- "Sim" --> F2["Exibir detalhamento da seção\ncom filtros de período"]
    F2 --> D3A
    D3A -- "Não" --> E(["◉ Fim"])
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef alerta fill:#FFF8E0,stroke:#C8973A,color:#3A2000
    classDef erro   fill:#FFF0F0,stroke:#CC4444,color:#330000
 
    class S start
    class E fim
    class A1,A2,B1,B2,C1,C2,C3,C4,C4B,D3,E2,F1,F2 acao
    class D1,C4A,D2,D4,E1,D3A dec
    class D5 alerta
    class A3 erro
```
 
---
 
## AD-07 — Compartilhamento e Exportação
 
Unifica os dois fluxos de saída de dados da plataforma: **compartilhamento** em redes sociais
com geração de card visual e **exportação** de fichas de degustação em PDF e de inventário
da adega em CSV ou PDF. Corresponde aos requisitos **FR33–FR35**.
 
```mermaid
flowchart TD
    S(["● Início"])
 
    S  --> A1["Usuário seleciona conteúdo\npara compartilhar ou exportar"]
    A1 --> D1{"O que deseja\nfazer?"}
 
    %% ── Compartilhar ────────────────────────────────
    D1 -- "Compartilhar\navaliação" --> B1["Sistema compõe card visual:\nfoto do rótulo · nota · comentário\n+ identidade visual do Vinho Notas"]
    B1 --> B2["Exibir pré-visualização do card"]
    B2 --> D2{"Card\naprovado?"}
    D2 -- "Editar" --> B3["Usuário ajusta comentário\nou nota de destaque"]
    B3 --> B1
    D2 -- "Compartilhar" --> B4{"Selecionar\ndestino"}
    B4 -- "Instagram" --> B5["Abrir compartilhamento\npara o Instagram"]
    B4 -- "WhatsApp"  --> B6["Abrir compartilhamento\npara o WhatsApp"]
    B4 -- "X (Twitter)" --> B7["Abrir compartilhamento\npara o X"]
    B5 --> Z1
    B6 --> Z1
    B7 --> Z1
 
    %% ── Exportar Ficha PDF ──────────────────────────
    D1 -- "Exportar ficha\nde degustação" --> C1["Selecionar ficha(s)\nde degustação"]
    C1 --> C2["Sistema renderiza PDF:\netapas · nota final · dados do vinho\n+ layout com identidade visual"]
    C2 --> C3["Exibir pré-visualização do PDF"]
    C3 --> C4["Gerar arquivo PDF final"]
    C4 --> C5["Download automático no dispositivo"]
    C5 --> Z1
 
    %% ── Exportar Inventário ─────────────────────────
    D1 -- "Exportar\ninventário da adega" --> D3{"Formato\ndesejado?"}
    D3 -- "CSV" --> D4["Gerar arquivo CSV\ncom todos os vinhos e campos"]
    D3 -- "PDF" --> D5["Gerar PDF de inventário\ncom layout formatado"]
    D4 --> D6["Download automático no dispositivo"]
    D5 --> D6
    D6 --> Z1
 
    Z1{"Realizar outra\noperação?"}
    Z1 -- "Sim" --> A1
    Z1 -- "Não" --> E(["◉ Fim"])
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef export fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
 
    class S start
    class E fim
    class A1,B1,B2,B3,B5,B6,B7,C1,C2,C3,C4,C5,D4,D5,D6 acao
    class D1,D2,B4,D3,Z1 dec
```
 
---
 
## AD-08 — Gamificação e Notificações
 
Representa dois fluxos de sistema executados em segundo plano: o **mecanismo de gamificação**
(verificação de conquistas e avanço de nível a cada ação do usuário) e o **sistema de
notificações** (envio de alertas personalizados via push e/ou e-mail).
Corresponde aos requisitos **FR36–FR40**.
 
```mermaid
flowchart TD
    S(["● Início\neventos de sistema"])
 
    S  --> PA{"Tipo de evento"}
 
    %% ── Gamificação ─────────────────────────────────
    PA -- "Usuário realizou\numa ação" --> A1["Registrar ação no histórico\n(cadastro · avaliação · degustação · etc.)"]
    A1  --> A2["Calcular pontuação acumulada"]
    A2  --> A3{"Marco de\nconquista atingido?"}
 
    A3  -- "Sim" --> A4["Identificar badge correspondente"]
    A4  --> A5["Conceder badge ao usuário"]
    A5  --> A6["Salvar conquista no perfil"]
    A6  --> B1
    A3  -- "Não" --> B1
 
    B1{"Novo nível\ndesbloqueado?"}
    B1  -- "Sim" --> B2["Atualizar nível do usuário:\nIniciante → Aprendiz → Enófilo\n→ Sommelier Amateur → Mestre Enófilo"]
    B2  --> B3["Exibir animação de avanço de nível"]
    B3  --> B4["Gerar notificação de conquista"]
    B4  --> C1
    B1  -- "Não" --> C1
 
    %% ── Notificações ────────────────────────────────
    PA -- "Gatilho de\nalerta agendado" --> N1["Sistema identifica alertas\ndo período (diário)"]
    N1  --> N2{"Há alertas\npendentes?"}
    N2  -- "Não" --> E(["◉ Fim"])
    N2  -- "Sim" --> N3["Verificar preferências\nde notificação do usuário"]
    N3  --> N4{"Notificações\nhabilitadas?"}
    N4  -- "Não" --> E
    N4  -- "Sim" --> N5{"Canal\npreferido?"}
 
    N5  -- "Push" --> N6["Enviar notificação push\npelo service worker do PWA"]
    N5  -- "E-mail" --> N7["Enviar e-mail personalizado\nvia serviço de e-mail"]
    N5  -- "Ambos" --> N6A["Enviar notificação push"]
    N6A --> N7A["Enviar e-mail personalizado"]
    N7A --> N8
    N6  --> N8
    N7  --> N8
 
    N8["Registrar envio no log\nde notificações"]
    N8  --> E
 
    C1  --> E(["◉ Fim"])
 
    classDef start  fill:#1A0610,color:#fff,stroke:#7A1E4A
    classDef fim    fill:#1A0610,color:#fff,stroke:#C8973A
    classDef acao   fill:#FDF5F8,stroke:#8B2252,color:#1A0A0F
    classDef dec    fill:#FFFBF0,stroke:#C8973A,color:#1A0A0F
    classDef gami   fill:#F0FFF0,stroke:#2E8B2E,color:#0A200A
    classDef notif  fill:#EFF6FF,stroke:#4A7C8B,color:#0A1A2A
 
    class S start
    class E fim
    class A1,A2,N1,N3,N8 acao
    class PA,A3,B1,N2,N4,N5 dec
    class A4,A5,A6,B2,B3,B4,C1 gami
    class N6,N6A,N7,N7A notif
```
 
---
 
## Resumo — Cobertura dos Diagramas de Atividades
 
| Diagrama | Módulo | Requisitos cobertos | Novidades em relação ao MVP |
|---|---|---|---|
| AD-01 | Autenticação e Conta | FR01–FR06 | OAuth Google/Apple, JWT, validação de maioridade |
| AD-02 | Cadastro de Vinho e Adega | FR07–FR13, FR41 | Scan de rótulo por câmera, adega virtual, alerta de ponto de consumo e conversão wishlist→adega |
| AD-03 | Avaliação Rápida | FR14–FR17 | Listas padronizadas, dois sistemas de pontuação, integração com gamificação |
| AD-04 | Ficha de Degustação Formal | FR18–FR22 | 4 etapas guiadas, degustação comparativa, exportação PDF |
| AD-05 | Sommelier Virtual / IA | FR23–FR27 | Harmonização por ingredientes, geração de menu, chat contextual, recomendações |
| AD-06 | Dashboard e Insights | FR28–FR32, FR42 | Gráficos de preferências, ranking pessoal, favorito do trimestre, alertas de adega, relatório de gastos |
| AD-07 | Compartilhamento e Exportação | FR33–FR35 | Geração de card visual, exportação CSV/PDF, compartilhamento em redes sociais |
| AD-08 | Gamificação e Notificações | FR36–FR40 | Sistema de badges, níveis, notificações push e e-mail configuráveis |
 
---
 
*Vinho Notas v2.0 — Diagramas de Atividades elaborados com base no TCC de Vanderlei Kleinschmidt (2024)*