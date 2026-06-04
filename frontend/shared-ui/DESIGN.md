# Documentação do Sistema de Design: Réserve Privée

## 1. Visão Geral e Estrela do Norte Criativa

Este sistema de design foi concebido para transcender a funcionalidade de um aplicativo utilitário, transformando-se em uma **"Curadoria Digital de Herança"**. O objetivo não é apenas listar vinhos, mas evocar a experiência sensorial de entrar em uma adega privada sob as ruas de Bordeaux: o silêncio, a temperatura controlada, a textura do papel de seda e a profundidade visual do carvalho e do vinho.

### A Estrela do Norte: "A Adega Editorial"
Diferente de interfaces de e-commerce tradicionais que prezam pela densidade de informações, este sistema utiliza o **espaço negativo como um artigo de luxo**. A estética é "Editorial de Alta Costura", onde a assimetria intencional e o contraste tipográfico comunicam autoridade e sofisticação.

*   **Quebra de Gabarito:** Evite grids rígidos de 2 ou 3 colunas. Use layouts sobrepostos, onde imagens de garrafas rompem as linhas dos containers, criando uma sensação de profundidade física (Z-index layering).
*   **Narrativa Visual:** Cada tela deve ser tratada como uma página de uma revista premium, onde o vinho é o protagonista absoluto.

---

## 2. Cores e Texturas

A paleta é centrada na profundidade do `primary_container` (#4A0E0E) e na leveza orgânica dos cremes. O luxo aqui é expresso através da sobriedade e do contraste tonal.

### A Regra do "Sem-Linha" (No-Line Rule)
**Proibição estrita:** É proibido o uso de bordas sólidas de 1px para separar seções. A diferenciação entre áreas deve ser feita exclusivamente através de:
1.  **Mudança de Tom:** Um card em `surface_container_lowest` sobre um fundo `surface`.
2.  **Espaçamento Negativo:** O uso generoso de margens para definir grupos de conteúdo.

### Hierarquia de Superfície e Aninhamento
Trate a interface como camadas de papel fino e vidro fosco:
*   **Base:** `surface` (#FCF9F3) para o fundo geral.
*   **Camada de Conteúdo:** `surface_container_low` para seções amplas.
*   **Destaque de Interação:** `surface_container_highest` para elementos que exigem foco imediato.

### A Regra de "Vidro e Gradiente"
Para evitar o visual "flat" e genérico:
*   **Efeito Sommelier:** Use Glassmorphism em elementos flutuantes (como filtros ou menus de navegação), utilizando `surface` com 80% de opacidade e `backdrop-blur` de 16px.
*   **Profundidade do Tinto:** Em botões principais e áreas de destaque, utilize um gradiente linear sutil de `primary_container` (#4A0E0E) para `primary` (#2A0002) em um ângulo de 135°, conferindo a viscosidade visual de um vinho encorpado.

---

## 3. Tipografia

A tipografia é o pilar da sofisticação editorial deste sistema. O contraste entre o clássico e o moderno dita o ritmo da leitura.

| Nível | Token | Fonte | Peso | Uso |
| :--- | :--- | :--- | :--- | :--- |
| **Display** | `display-lg` | Noto Serif | 400 (Regular) | Títulos de coleções raras e safras especiais. |
| **Headline** | `headline-md` | Noto Serif | 500 (Medium) | Cabeçalhos de seção e nomes de vinícolas. |
| **Title** | `title-lg` | Manrope | 600 (SemiBold) | Títulos de interface, cards e modais. |
| **Body** | `body-lg` | Manrope | 400 (Regular) | Descrições de notas de degustação e história. |
| **Label** | `label-md` | Manrope | 700 (Bold) | Metadados (Safra, Teor Alcoólico, Região). |

**Diretriz Editorial:** Use `display-lg` com kerning (espaçamento entre letras) levemente reduzido para evocar o design de rótulos europeus clássicos.

---

## 4. Elevação e Profundidade

O sistema abandona sombras pesadas em favor do **Empilhamento Tonal**.

*   **Princípio de Camadas:** A profundidade nasce da luz natural. Um objeto "sobe" na interface ao tornar-se mais claro (usando `surface_container_lowest`) em relação ao fundo.
*   **Sombras Ambientes:** Se um elemento precisar flutuar (ex: botão de ação flutuante), use uma sombra extra-difusa: `0px 24px 48px rgba(28, 28, 24, 0.06)`. A sombra nunca deve ser cinza puro; ela deve conter um rastro do tom `on-surface`.
*   **Ghost Border:** Para acessibilidade em campos de input, use a `outline_variant` com apenas 15% de opacidade. O objetivo é que a borda seja sentida, não vista.

---

## 5. Componentes de Interface

### Botões (CTAs)
*   **Primário:** Fundo gradiente (Primary para Primary Container), sem bordas, cantos `md` (0.375rem). Texto em `on_primary`.
*   **Secundário:** Apenas texto em `primary_container` com um sublinhado de 2px posicionado 4px abaixo da linha de base, estilo editorial.

### Cards de Vinho
*   **Regra de Ouro:** Proibido o uso de linhas divisórias. O card é definido pelo contraste entre `surface_container_low` e o fundo.
*   **Imagem:** A garrafa deve "vazar" para fora do card na parte superior (negative margin), criando um efeito 3D de prateleira física.

### Chips de Filtro (Região, Safra, Uva)
*   Formato pill (`full` roundedness). Estado inativo em `surface_container_high`. Estado ativo em `primary_container` com texto em `on_primary`.

### Inputs de Busca/Texto
*   Estilo minimalista. Apenas uma linha fina na base (utilizando `outline_variant` a 20% de opacidade) que se torna `primary_container` quando focada. O rótulo (label) usa `label-sm` em `secondary`.

---

## 6. Do's and Don'ts (Práticas Recomendadas)

### ✅ Fazer (Do)
*   **Respiro:** Deixe pelo menos 32px de margem lateral em telas de leitura para reforçar o estilo editorial.
*   **Imagens de Alta Qualidade:** Use fotografias com iluminação dramática (chiaroscuro) para os vinhos.
*   **Micro-interações:** Transições suaves de opacidade (fade) ao carregar novos elementos, simulando a elegância de um serviço de sommelier.

### ❌ Não Fazer (Don't)
*   **Cantos Excessivamente Arredondados:** Evite o uso de `full` roundedness em botões ou cards quadrados; isso quebra a seriedade do luxo. Use `md` ou `lg`.
*   **Cores Vibrantes de Erro:** Nunca use um vermelho "alerta" saturado. Use o token `error` (#BA1A1A) que é um tom de rubi profundo, mantendo a harmonia cromática.
*   **Grids Genéricos:** Evite alinhar tudo ao centro. A assimetria (ex: texto alinhado à esquerda com uma imagem deslocada à direita) gera um visual mais premium.

---

**Nota do Diretor:** Este sistema não é apenas um guia de componentes; é um manifesto de prestígio. Cada pixel deve refletir a paciência e a tradição de uma safra envelhecida. Se parecer um "app comum", adicione mais espaço em branco e reduza os pesos das linhas.