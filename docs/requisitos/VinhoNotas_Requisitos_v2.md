**VINHO NOTAS**

_Tornando a Experiência Enológica Acessível e Agradável_

**Documento de Requisitos - Versão 2.0**

Análise e Reformulação de Requisitos Funcionais,

Não Funcionais e Personas

**Vanderlei Kleinschmidt**

2025

# **1\. Apresentação**

Este documento apresenta a reformulação completa dos requisitos do sistema Vinho Notas, uma aplicação web progressiva (PWA) voltada para enófilos amadores e entusiastas do universo dos vinhos. A nova versão evolui o MVP original, incorporando funcionalidades modernas, experiência de usuário aprimorada, inteligência artificial e recursos de comunidade, tornando a plataforma mais completa, atrativa e competitiva.

O Vinho Notas continua fiel à sua proposta de valor central: facilitar a jornada de descoberta e organização no mundo dos vinhos, sem transformar-se em um e-commerce ou rede social. Na versão 2.0, a plataforma incorpora funcionalidades que respondem às expectativas de um público cada vez mais digital e exigente.

Para contextualizar a revisão, é importante destacar as limitações identificadas no MVP original:

- Requisitos funcionais enxutos, com apenas 10 funcionalidades básicas.
- Ausência de recursos de inteligência artificial além da harmonização.
- Falta de dashboard e visualizações de dados pessoais.
- Inexistência de gamificação ou engajamento progressivo do usuário.
- Ausência de exportação de dados e funcionalidades de compartilhamento.
- Requisitos não funcionais incompletos em termos de segurança, acessibilidade e desempenho.

# **2\. Personas**

A definição de personas é fundamental para orientar o desenvolvimento centrado no usuário. Conforme Caroli (2015, p. 62), "uma persona representa um usuário do sistema, descrevendo não só o seu papel, mas também suas necessidades específicas". As personas a seguir foram revisadas e enriquecidas a partir das originais do TCC, incorporando comportamentos digitais contemporâneos, novos perfis de consumidor e motivações mais precisas.

A versão 2.0 mantém as três personas originais - Marina, Ricardo e André - com perfis revisados e aprofundados, e adiciona uma quarta persona que representa um segmento emergente e estratégico: a consumidora jovem e conectada da Geração Z.

## **2.1 Persona 1 - Marina Souza**

| **Nome**              | Marina Souza                                  |
| --------------------- | --------------------------------------------- |
| **Idade**             | 26 anos                                       |
| **Profissão**         | UX Designer em agência de comunicação digital |
| **Localização**       | Florianópolis, SC                             |
| **Perfil no sistema** | Enófila                                       |

### **Quem é Marina**

Marina é uma profissional criativa, habituada a interfaces digitais bem projetadas e experiências intuitivas. Recentemente começou a explorar o universo dos vinhos por influência de amigos e das redes sociais. Ela aprecia vinhos brancos leves e espumantes, mas ainda está construindo seu paladar e seu vocabulário enológico. Usa o smartphone como principal dispositivo para tudo, inclusive aplicativos de organização e aprendizado.

### **Comportamento digital**

- Consome conteúdo sobre vinhos no Instagram e YouTube.
- Usa aplicativos de produtividade e organização no dia a dia.
- Prefere onboarding visual, rápido e sem formulários extensos.
- Já utilizou o Vivino para escanear rótulos em supermercados.

### **Necessidades e objetivos**

- Registrar o que bebeu de forma simples e visual, sem jargões técnicos.
- Descobrir novos rótulos baseados no que já provou e gostou.
- Aprender sobre harmonização de forma prática e contextualizada.
- Compartilhar suas descobertas de forma esteticamente agradável.

### **Frustrações e dores**

- Sente-se intimidada por apps com muitos campos obrigatórios.
- Perde o histórico dos vinhos que provou por falta de uma ferramenta centralizada.
- Não sabe por onde começar quando quer aprender mais sobre vinhos.

## **2.2 Persona 2 - Ricardo Alves**

| **Nome**              | Ricardo Alves                            |
| --------------------- | ---------------------------------------- |
| **Idade**             | 38 anos                                  |
| **Profissão**         | Gerente de Projetos de TI                |
| **Localização**       | Curitiba, PR                             |
| **Perfil no sistema** | Enófilo / candidato a Sommelier parceiro |

### **Quem é Ricardo**

Ricardo é um profissional de tecnologia apaixonado por vinhos há cerca de oito anos. Frequenta eventos de degustação, assina um clube de vinhos e tem uma adega doméstica com cerca de 60 garrafas. Possui conhecimento intermediário-avançado, domina o vocabulário enológico e gosta de compartilhar suas experiências com amigos em jantares temáticos. Valoriza ferramentas bem construídas e funcionalmente ricas.

### **Comportamento digital**

- Pesquisa extensivamente antes de comprar um vinho: lê blogs, avaliações do Vivino e Robert Parker.
- Mantém planilhas para controlar sua adega e histórico de degustações.
- Usa o celular e o laptop com a mesma frequência.
- Participa de grupos no WhatsApp e Telegram sobre vinhos.

### **Necessidades e objetivos**

- Organizar sua adega virtual com posições, safras e pontos de guarda.
- Criar fichas de degustação completas e exportáveis em PDF.
- Ter um histórico consolidado com gráficos de preferências.
- Planejar eventos de degustação com múltiplos vinhos e convidados.

### **Frustrações e dores**

- As planilhas que mantém são difíceis de acessar no celular.
- Não tem uma visão consolidada das suas preferências ao longo do tempo.
- Sente falta de um assistente virtual para tirar dúvidas rápidas sobre harmonização e serviço.

## **2.3 Persona 3 - André Fernandes**

| **Nome**              | André Fernandes                                    |
| --------------------- | -------------------------------------------------- |
| **Idade**             | 34 anos                                            |
| **Profissão**         | Consultor Financeiro e Sócio em escritório próprio |
| **Localização**       | São Paulo, SP                                      |
| **Perfil no sistema** | Enófilo avançado / futuro Parceiro                 |

### **Quem é André**

André é um entusiasta sofisticado que vê o vinho como uma forma de arte e um investimento cultural. Participa ativamente de confrarias locais, realiza viagens enoturísticas para regiões do Novo e Velho Mundo e tem uma adega com mais de 150 garrafas. Possui orçamento elevado para investimento em vinhos e busca constantemente experiências únicas. Está interessado em eventualmente se tornar um parceiro da plataforma, indicando vinhos para sua rede de relacionamentos.

### **Comportamento digital**

- Compartilha experiências enológicas no Instagram e LinkedIn.
- Lê publicações especializadas como Wine Spectator e Decanter.
- Usa aplicativos premium e está disposto a pagar por funcionalidades exclusivas.
- Acessa a plataforma principalmente via desktop.

### **Necessidades e objetivos**

- Construir um portfólio digital de degustações com fichas técnicas detalhadas.
- Ter acesso a relatórios avançados e exportação de dados.
- Gerir sua adega com alertas de ponto de consumo ideal.
- Ter um canal privilegiado de recomendações para sua rede de contatos.

### **Frustrações e dores**

- Não encontra uma ferramenta que atenda tanto ao nível técnico quanto à experiência visual que espera.
- Perde degustações não registradas por falta de praticidade no momento.
- Quer organizar sua adega mas não encontra uma solução integrada e elegante.

## **2.4 Persona 4 - Camila Torres (nova)**

| **Nome**              | Camila Torres                                           |
| --------------------- | ------------------------------------------------------- |
| **Idade**             | 24 anos                                                 |
| **Profissão**         | Estudante de Gastronomia e criadora de conteúdo digital |
| **Localização**       | Belo Horizonte, MG                                      |
| **Perfil no sistema** | Enófila / influenciadora em formação                    |

### **Quem é Camila**

Camila é uma estudante de gastronomia que descobriu sua paixão por vinhos durante as aulas de harmonização. Mantém um perfil no Instagram e no TikTok sobre gastronomia, onde compartilha receitas e experiências à mesa. Está construindo sua identidade como criadora de conteúdo e enxerga os vinhos como parte fundamental da narrativa que quer contar. Representa a nova geração de consumidores que chegam ao mundo do vinho pela via digital e visual.

### **Comportamento digital**

- Nativa digital: smartphone é sua extensão natural.
- Descobre novos rótulos pelo TikTok, Instagram Reels e indicações de criadores de conteúdo.
- Cria conteúdo visual de alta qualidade para redes sociais.
- Espera que apps gerem cards e conteúdos prontos para compartilhar.

### **Necessidades e objetivos**

- Registrar degustações de forma rápida e visualmente rica diretamente do celular.
- Gerar cards visuais com avaliações para compartilhar no Instagram e WhatsApp.
- Construir um portfólio de experiências que reforce sua credibilidade como criadora.
- Aprender sobre vinhos de forma interativa, contextual e sem termos excessivamente técnicos.

### **Frustrações e dores**

- Apps de vinho existentes são visualmente datados ou muito técnicos.
- Não tem uma ferramenta que integre registro de degustação com produção de conteúdo.
- Quer registrar o vinho enquanto ainda está à mesa, mas os fluxos costumam ser longos demais.

# **3\. Requisitos Funcionais**

Os requisitos funcionais descrevem as funcionalidades e comportamentos esperados do sistema Vinho Notas versão 2.0. Em relação à versão original do MVP, o número de requisitos funcionais passou de 10 para 40, organizados em 9 módulos temáticos. Essa expansão reflete a evolução da plataforma de um MVP básico para um produto mais completo, moderno e competitivo.

Cada requisito está associado às personas que mais se beneficiam daquela funcionalidade, permitindo priorização orientada ao usuário durante o planejamento das sprints.

## **3.1 Módulo: Autenticação e Gestão de Conta**

Requisitos relacionados ao acesso, cadastro e gerenciamento da conta do usuário. A nova versão moderniza o processo de registro, tornando-o mais ágil e seguro, incorporando autenticação social e separando os dados de acesso dos dados pessoais.

| **Req.** | **Descrição**                                                                                                                                                                                                                                                            | **Personas**                     |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------- |
| **FR01** | O sistema deve permitir que o usuário se cadastre informando nome completo, e-mail válido e senha. O cadastro deve incluir a confirmação da maioridade (18 anos ou mais) mediante informação da data de nascimento.                                                      | _Marina, Ricardo, André, Camila_ |
| **FR02** | O sistema deve oferecer autenticação social via Google e Apple ID como alternativa ao cadastro por e-mail e senha, dispensando formulários extensos para novos usuários.                                                                                                 | _Marina, Camila_                 |
| **FR03** | O sistema deve permitir que o usuário faça login com e-mail e senha ou através dos provedores de autenticação social configurados. Deve existir a opção de recuperação de senha por e-mail.                                                                              | _Todos_                          |
| **FR04** | O sistema deve bloquear o acesso de usuários menores de 18 anos, realizando a validação da data de nascimento durante o cadastro e exibindo uma mensagem de impedimento quando necessário.                                                                               | _Todos_                          |
| **FR05** | O sistema deve disponibilizar uma tela de perfil onde o usuário pode visualizar e editar seus dados pessoais, preferências de notificação, idioma e tema (claro/escuro).                                                                                                 | _Todos_                          |
| **FR06** | O sistema deve suportar os perfis: Enófilo (acesso gratuito a todas as funcionalidades da versão 1.0), Sommelier (acesso profissional, com ferramentas avançadas, mediante parceria) e Parceiro (vinícola, e-commerce ou importador, com acesso a relatórios agregados). | _Ricardo, André_                 |

## **3.2 Módulo: Gestão de Vinhos**

Requisitos relacionados ao cadastro, consulta, organização e manutenção do acervo de vinhos do usuário. Esta é a funcionalidade central da plataforma e foi significativamente expandida para contemplar a adega virtual e o registro por câmera.

| **Req.** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                        | **Personas**              |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------- |
| **FR07** | O usuário deve poder cadastrar um vinho informando obrigatoriamente o rótulo. Os demais campos são opcionais: produtor, país de origem, região, safra, tipo (tinto, branco, rosé, espumante, licoroso, sobremesa), cor, classificação, uva(s), graduação alcoólica, volume, temperatura de serviço, tempo de guarda, preço, local de compra, data de compra e notas de harmonização. | _Todos_                   |
| **FR08** | O sistema deve permitir que o usuário escaneie o código de barras ou QR code de uma garrafa de vinho utilizando a câmera do dispositivo. Quando o rótulo for identificado, os campos de cadastro devem ser preenchidos automaticamente, cabendo ao usuário confirmar ou editar as informações.                                                                                       | _Marina, Camila_          |
| **FR09** | Os vinhos cadastrados devem ser exibidos em uma tela de listagem com suporte a busca textual e filtros por tipo, país, uva, safra, faixa de preço e avaliação. O usuário deve poder alternar entre visualização em grade e em lista.                                                                                                                                                 | _Todos_                   |
| **FR10** | O usuário deve poder editar e excluir qualquer vinho cadastrado por ele. Ao excluir um vinho que possua avaliações ou degustações associadas, o sistema deve exibir um aviso de impacto antes de confirmar a exclusão.                                                                                                                                                               | _Todos_                   |
| **FR11** | O sistema deve oferecer uma funcionalidade de adega virtual, onde o usuário pode informar a quantidade de garrafas disponíveis de cada vinho e, opcionalmente, a posição física na adega (prateleira/coluna). O sistema deve exibir um painel da adega organizado visualmente.                                                                                                       | _Ricardo, André_          |
| **FR12** | O usuário deve poder criar uma lista de desejos (wishlist) com vinhos que deseja provar futuramente, podendo adicionar observações e uma estimativa de preço.                                                                                                                                                                                                                        | _Marina, Camila, Ricardo_ |
| **FR13** | O sistema deve permitir ao usuário exportar a lista de vinhos cadastrados em formato CSV ou PDF, para fins de backup e referência.                                                                                                                                                                                                                                                   | _Ricardo, André_          |

## **3.3 Módulo: Avaliação Rápida**

Requisitos relacionados ao registro simplificado de impressões sobre um vinho. A avaliação rápida é diferente da ficha de degustação formal: ela é pensada para o momento do consumo casual, com menos campos e maior agilidade.

| **Req.** | **Descrição**                                                                                                                                                                                                                                                     | **Personas**             |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| **FR14** | O usuário deve poder avaliar um vinho previamente cadastrado, atribuindo-lhe uma nota de 1 a 5 estrelas (ou de 0 a 100 pontos, conforme preferência configurada no perfil). O campo de comentário livre é opcional.                                               | _Todos_                  |
| **FR15** | Na avaliação rápida, o usuário pode registrar opcionalmente: a data da degustação, os aspectos visuais percebidos (cor, brilho, viscosidade), os aromas identificados (frutas, especiarias, madeira, floral, terroso), os sabores e a impressão de final de boca. | _Ricardo, André, Camila_ |
| **FR16** | As avaliações devem ser listadas em uma tela dedicada, ordenáveis por data, nota ou rótulo, com opção de edição e exclusão.                                                                                                                                       | _Todos_                  |
| **FR17** | O sistema deve exibir o histórico de avaliações de um vinho em sua ficha de detalhes, permitindo que o usuário acompanhe a evolução das suas impressões ao longo do tempo.                                                                                        | _Ricardo, André_         |

## **3.4 Módulo: Ficha de Degustação**

Requisitos relacionados ao processo formal de degustação, guiado pelo sistema. Diferente da avaliação rápida, a ficha de degustação segue um protocolo estruturado com as quatro etapas clássicas da análise sensorial.

| **Req.** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                          | **Personas**             |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------ |
| **FR18** | O usuário deve poder iniciar uma ficha de degustação selecionando um vinho cadastrado, definindo a data, o tipo (vertical, horizontal, temática, às cegas, de harmonização ou comparativa) e, opcionalmente, o local e os participantes.                                                                                                                                                               | _Ricardo, André, Camila_ |
| **FR19** | A ficha de degustação deve guiar o usuário pelas quatro etapas da análise sensorial: (1) Inspeção Visual - cor, intensidade, brilho, turbidez e viscosidade; (2) Análise Olfativa - intensidade, aromas primários, secundários e terciários; (3) Análise Gustativa - ataque, acidez, taninos, corpo, álcool, doçura e persistência; (4) Conclusão - avaliação geral, potencial de guarda e nota final. | _Ricardo, André, Camila_ |
| **FR20** | O sistema deve apresentar listas de termos padronizados para cada etapa da degustação (ex.: aromas de frutas vermelhas, especiarias, madeira), permitindo que o usuário selecione os que identifica sem precisar digitar manualmente.                                                                                                                                                                  | _Marina, Camila_         |
| **FR21** | As fichas de degustação registradas devem ser listadas com busca e filtros, e o usuário deve poder editar, excluir ou exportar cada ficha individualmente em formato PDF.                                                                                                                                                                                                                              | _Ricardo, André_         |
| **FR22** | O sistema deve suportar degustações comparativas, permitindo que o usuário vincule múltiplos vinhos a uma mesma sessão de degustação, com fichas individuais para cada vinho e um campo de comparação geral.                                                                                                                                                                                           | _André_                  |

## **3.5 Módulo: Sommelier Virtual (IA)**

Requisitos relacionados às funcionalidades de inteligência artificial generativa incorporadas à plataforma. A versão 2.0 expande significativamente o uso de IA para além da harmonização, criando um verdadeiro assistente enológico.

| **Req.** | **Descrição**                                                                                                                                                                                                                                                | **Personas**              |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------- |
| **FR23** | O sistema deve oferecer sugestões de harmonização para um vinho selecionado, indicando alimentos, ingredientes e preparos culinários que combinam com o perfil do vinho, utilizando IA generativa (LLM).                                                     | _Todos_                   |
| **FR24** | O sistema deve ser capaz de sugerir um menu completo (entrada, prato principal e sobremesa) harmonizado com um vinho selecionado, com base em informações sobre o estilo do vinho fornecidas pelo usuário ou extraídas do cadastro.                          | _Ricardo, André, Camila_  |
| **FR25** | O sistema deve disponibilizar um chat contextual com o sommelier virtual, onde o usuário pode fazer perguntas livres sobre um vinho, como temperatura ideal de serviço, tipo de taça recomendada, tempo de aeração e informações sobre a vinícola ou região. | _Todos_                   |
| **FR26** | Com base no histórico de avaliações do usuário, o sistema deve sugerir novos rótulos para descoberta ("Você pode gostar de..."), utilizando o perfil de preferências identificado pelo algoritmo.                                                            | _Marina, Ricardo, Camila_ |
| **FR27** | O sistema deve permitir que o usuário insira os ingredientes de uma refeição e receba sugestões de vinhos do seu acervo que harmonizem com ela.                                                                                                              | _Ricardo, André_          |

## **3.6 Módulo: Dashboard e Insights**

Requisitos relacionados à visualização consolidada dos dados do usuário. O dashboard é um recurso novo na versão 2.0, respondendo à necessidade de usuários mais avançados de terem uma visão panorâmica das suas preferências e histórico.

| **Req.** | **Descrição**                                                                                                                                                                                                                   | **Personas**     |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| **FR28** | O sistema deve exibir um painel inicial (dashboard) com um resumo das atividades recentes do usuário: últimas degustações, últimas avaliações, número de vinhos na adega e destaques do histórico.                              | _Todos_          |
| **FR29** | O dashboard deve apresentar gráficos e indicadores estatísticos das preferências do usuário: distribuição por tipo de vinho, por país de origem, por uva, por faixa de preço e evolução das notas atribuídas ao longo do tempo. | _Ricardo, André_ |
| **FR30** | O sistema deve exibir os vinhos mais bem avaliados pelo usuário (Top 5 ou Top 10), funcionando como um ranking pessoal de referência.                                                                                           | _Todos_          |
| **FR31** | O sistema deve permitir o registro do valor pago por cada garrafa e exibir relatórios de gasto por período (mensal/anual), acompanhados de gráficos de evolução.                                                                | _André, Ricardo_ |
| **FR32** | Para vinhos cadastrados na adega com informação de tempo de guarda e safra, o sistema deve calcular e exibir uma estimativa do período ideal de consumo e alertar quando um vinho estiver próximo do seu pico.                  | _André_          |

## **3.7 Módulo: Compartilhamento e Exportação**

Requisitos relacionados ao compartilhamento de conteúdo e à portabilidade dos dados. Esses recursos respondem diretamente ao comportamento das novas personas e ao uso de redes sociais.

| **Req.** | **Descrição**                                                                                                                                                                                                                                                            | **Personas**     |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------- |
| **FR33** | O sistema deve permitir que o usuário gere um card visual padronizado com as informações de uma avaliação ou degustação (foto do rótulo, nota, comentário principal e identidade visual do Vinho Notas) para compartilhamento em redes sociais (Instagram, WhatsApp, X). | _Camila, Marina_ |
| **FR34** | O usuário deve poder exportar uma ficha de degustação individual em formato PDF com identidade visual do sistema, incluindo todas as etapas preenchidas, nota final e informações do vinho.                                                                              | _Ricardo, André_ |
| **FR35** | O sistema deve permitir que o usuário exporte o inventário completo da adega em formato CSV ou PDF.                                                                                                                                                                      | _André, Ricardo_ |

## **3.8 Módulo: Notificações e Lembretes**

Requisitos relacionados ao sistema de comunicação proativa com o usuário. As notificações são um recurso importante para o engajamento contínuo com a plataforma.

| **Req.** | **Descrição**                                                                                                                                                                                                                 | **Personas** |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| **FR36** | O sistema deve enviar notificações push (para usuários que instalaram o PWA) e/ou e-mail com lembretes personalizados, como: vinho atingindo o ponto ideal de consumo, sugestão de nova degustação e novidades da plataforma. | _Todos_      |
| **FR37** | O usuário deve poder configurar suas preferências de notificação, escolhendo quais tipos deseja receber e em qual canal (push, e-mail ou ambos).                                                                              | _Todos_      |

## **3.9 Módulo: Gamificação e Engajamento**

Requisitos relacionados ao sistema de progresso e conquistas do usuário. A gamificação é uma funcionalidade nova na versão 2.0, desenhada para estimular o engajamento contínuo e a evolução do conhecimento enológico.

| **Req.** | **Descrição**                                                                                                                                                                                                                                      | **Personas**              |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| **FR38** | O sistema deve implementar um sistema de níveis baseado na atividade do usuário: Iniciante (0-10 registros), Aprendiz (11-30), Enófilo (31-80), Sommelier Amateur (81-150) e Mestre Enófilo (151+). O nível deve ser exibido no perfil do usuário. | _Marina, Camila, Ricardo_ |
| **FR39** | O sistema deve conceder conquistas (badges) por marcos atingidos, como: primeiro vinho cadastrado, primeira degustação completa, 10 países diferentes avaliados, uso do sommelier virtual pela primeira vez, entre outros.                         | _Marina, Camila_          |
| **FR40** | O usuário deve poder visualizar seu progresso, conquistas obtidas e conquistas futuras em uma seção dedicada no perfil.                                                                                                                            | _Marina, Camila_          |

# **4\. Requisitos Não Funcionais**

Os requisitos não funcionais (NFR) estabelecem as restrições de qualidade, desempenho, segurança e operação do sistema. Conforme Pressman e Maxim (2021, p. 248), um requisito não funcional "pode ser descrito como um atributo de qualidade, de desempenho, de segurança ou como uma restrição geral em um sistema". A versão 2.0 expande os 5 requisitos originais para 18, cobrindo categorias fundamentais para um produto moderno e responsável.

| **Req.**  | **Categoria**                 | **Descrição**                                                                                                                                                                                                                                                                              |
| --------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **NFR01** | **Legal / Acesso**            | O acesso ao sistema é permitido exclusivamente a usuários com 18 anos ou mais. A validação deve ocorrer durante o cadastro e ser verificada via token de sessão autenticado.                                                                                                               |
| **NFR02** | **Legal / Privacidade**       | O armazenamento, tratamento e descarte de dados pessoais deve estar em conformidade com a Lei Geral de Proteção de Dados (LGPD - Lei nº 13.709/2018). O sistema deve oferecer funcionalidades para o usuário visualizar, exportar e solicitar a exclusão de seus dados.                    |
| **NFR03** | **Disponibilidade / Offline** | A aplicação deve funcionar em modo offline após a instalação como PWA, permitindo a consulta e o registro de informações. A sincronização com o servidor deve ocorrer automaticamente quando a conexão for restabelecida, utilizando Service Workers e estratégias de cache.               |
| **NFR04** | **Arquitetura**               | A comunicação entre frontend e backend deve ocorrer exclusivamente via APIs RESTful seguindo o padrão BFF (Backend For Frontend). O BFF é responsável por orquestrar as chamadas aos micro serviços e entregar ao frontend respostas otimizadas para o contexto de uso.                    |
| **NFR05** | **Frontend**                  | A aplicação deve ser construída como uma Single Page Application (SPA) na forma de uma Progressive Web Application (PWA), permitindo instalação no dispositivo do usuário em desktop e mobile sem necessidade de loja de aplicativos.                                                      |
| **NFR06** | **Responsividade**            | A interface deve seguir a abordagem mobile-first, adaptando-se de forma fluida a diferentes tamanhos de tela: smartphones (320-767px), tablets (768-1023px) e desktops (1024px+). Todos os fluxos críticos devem ser completamente utilizáveis em smartphones.                             |
| **NFR07** | **Desempenho**                | A aplicação deve atingir pontuação mínima de 85 no Google Lighthouse para as categorias Performance, Acessibilidade e Boas Práticas. O tempo de carregamento inicial (First Contentful Paint) não deve exceder 2,5 segundos em conexão 4G.                                                 |
| **NFR08** | **Segurança - Autenticação**  | Toda comunicação entre cliente e servidor deve utilizar HTTPS (TLS 1.2 ou superior). A autenticação deve ser baseada em JWT (JSON Web Token) com tempo de expiração configurável. O refresh token deve ser armazenado de forma segura (HTTP-only cookie).                                  |
| **NFR09** | **Segurança - Autorização**   | O sistema deve implementar controle de acesso baseado em papéis (RBAC). Cada endpoint da API deve validar o perfil do usuário autenticado antes de processar a requisição. Dados de um usuário não devem ser acessíveis por outro.                                                         |
| **NFR10** | **Segurança - Dados**         | Senhas de usuários devem ser armazenadas utilizando algoritmo de hash seguro (bcrypt ou Argon2). Nenhum dado sensível deve ser retornado em logs ou respostas de API desnecessariamente.                                                                                                   |
| **NFR11** | **Acessibilidade**            | A interface deve seguir as diretrizes WCAG 2.1 no nível AA, garantindo compatibilidade com leitores de tela, contraste de cores adequado, navegação por teclado e suporte a tecnologias assistivas.                                                                                        |
| **NFR12** | **Internacionalização**       | A aplicação deve suportar múltiplos idiomas, sendo os inicialmente obrigatórios: Português Brasileiro (padrão), Inglês e Espanhol. Textos do sistema não devem ser codificados em código-fonte (hardcoded), devendo utilizar arquivos de tradução.                                         |
| **NFR13** | **Escalabilidade**            | A arquitetura de micro serviços deve permitir o escalonamento horizontal independente de cada serviço. Cada micro serviço deve ser empacotado em container Docker e orquestrável via Docker Compose (desenvolvimento) ou Kubernetes (produção).                                            |
| **NFR14** | **Observabilidade**           | O sistema deve gerar logs estruturados em formato JSON para todas as operações críticas. Deve haver suporte a rastreamento distribuído (tracing) para identificação de gargalos entre os micro serviços. Métricas de uso devem ser coletadas e expostas para monitoramento.                |
| **NFR15** | **Integração Contínua**       | O repositório de código deve possuir pipelines de CI/CD configurados (GitHub Actions ou equivalente) que automatizem: compilação, execução de testes, análise estática de código (SonarQube ou equivalente) e publicação em ambiente de homologação.                                       |
| **NFR16** | **Qualidade - Testes**        | O código backend deve manter cobertura mínima de 80% de métodos e classes testados, medida pelo JaCoCo. O frontend deve possuir testes de componentes com Jest/Testing Library. Testes de integração devem cobrir os principais fluxos de negócio.                                         |
| **NFR17** | **Usabilidade**               | O fluxo de cadastro do primeiro vinho deve ser concluído em no máximo 5 etapas. A ficha de degustação rápida deve poder ser preenchida em menos de 2 minutos. O sistema deve fornecer feedback visual imediato para todas as ações do usuário (loading states, mensagens de sucesso/erro). |
| **NFR18** | **Sustentabilidade do Dado**  | O sistema deve realizar backup automático dos dados dos usuários com frequência mínima diária. Em caso de exclusão de conta, o usuário deve ter a opção de exportar todos os seus dados antes da remoção definitiva, conforme exigido pela LGPD.                                           |

# **5\. Mapeamento de Casos de Uso**

O quadro a seguir apresenta o mapeamento dos principais casos de uso identificados a partir dos requisitos funcionais, indicando os módulos, atores envolvidos e os requisitos correspondentes. Este mapeamento serve como base para a elaboração dos diagramas UML na fase de design.

| **UC**   | **Caso de Uso**                      | **Ator(es)**         | **Requisitos**               |
| -------- | ------------------------------------ | -------------------- | ---------------------------- |
| **UC01** | Autenticar no sistema                | _Todos os usuários_  | FR01, FR02, FR03, FR04       |
| **UC02** | Gerenciar perfil de usuário          | _Todos os usuários_  | FR05, FR06                   |
| **UC03** | Cadastrar vinho                      | _Todos os usuários_  | FR07, FR08                   |
| **UC04** | Escanear rótulo de vinho             | _Enófilo_            | FR08                         |
| **UC05** | Consultar e filtrar vinhos           | _Todos os usuários_  | FR09, FR10                   |
| **UC06** | Gerenciar adega virtual              | _Enófilo, Sommelier_ | FR11                         |
| **UC07** | Gerenciar wishlist de vinhos         | _Todos os usuários_  | FR12                         |
| **UC08** | Fazer avaliação rápida de vinho      | _Todos os usuários_  | FR14, FR15, FR16             |
| **UC09** | Criar ficha de degustação            | _Enófilo, Sommelier_ | FR18, FR19, FR20             |
| **UC10** | Gerenciar degustações                | _Todos os usuários_  | FR21, FR22                   |
| **UC11** | Consultar harmonizações por IA       | _Todos os usuários_  | FR23, FR24, FR27             |
| **UC12** | Interagir com sommelier virtual      | _Todos os usuários_  | FR25                         |
| **UC13** | Receber recomendações personalizadas | _Todos os usuários_  | FR26                         |
| **UC14** | Visualizar dashboard e insights      | _Todos os usuários_  | FR28, FR29, FR30, FR31, FR32 |
| **UC15** | Compartilhar avaliação nas redes     | _Todos os usuários_  | FR33                         |
| **UC16** | Exportar dados e fichas              | _Enófilo, Sommelier_ | FR34, FR35                   |
| **UC17** | Gerenciar notificações               | _Todos os usuários_  | FR36, FR37                   |
| **UC18** | Acompanhar progresso e conquistas    | _Todos os usuários_  | FR38, FR39, FR40             |

# **6\. Resumo Comparativo - MVP vs. Versão 2.0**

O quadro abaixo resume as principais evoluções entre o MVP original (v1.0) e a proposta reformulada (v2.0), evidenciando o crescimento em escopo, qualidade e modernidade da solução.

| **Dimensão**               | **MVP - v1.0**            | **Reformulado - v2.0**                     |
| -------------------------- | ------------------------- | ------------------------------------------ |
| **Personas**               | 3 personas                | **4 personas (inclui Geração Z)**          |
| **Req. Funcionais**        | 10 requisitos, 4 módulos  | **40 requisitos, 9 módulos**               |
| **Req. Não Funcionais**    | 5 requisitos              | **18 requisitos**                          |
| **Casos de Uso**           | 4 diagramas UC            | **18 casos de uso mapeados**               |
| **Autenticação**           | E-mail e senha numérica   | **OAuth (Google/Apple) + JWT**             |
| **IA / Sommelier Virtual** | Harmonização básica       | **Chat, recomendações, menu completo**     |
| **Dashboard**              | Não existia               | **Gráficos, insights e estatísticas**      |
| **Adega Virtual**          | Parcialmente mencionada   | **Módulo completo com alertas**            |
| **Scan de Rótulo**         | Não existia               | **Via câmera com preenchimento auto**      |
| **Compartilhamento**       | Não existia               | **Cards para redes sociais**               |
| **Gamificação**            | Não existia               | **Níveis e conquistas (badges)**           |
| **Acessibilidade**         | Não especificada          | **WCAG 2.1 AA**                            |
| **Internacionalização**    | Não especificada          | **PT-BR, EN, ES**                          |
| **Exportação**             | CSV básico                | **CSV e PDF com identidade visual**        |
| **Testes**                 | Mín. 80% cobertura JaCoCo | **80% backend + testes de comp. frontend** |

# **7\. Considerações Finais**

Este documento de requisitos versão 2.0 representa uma evolução substancial do projeto Vinho Notas. A expansão dos requisitos funcionais e não funcionais, aliada à reformulação das personas, cria uma base sólida para o desenvolvimento de um produto digital moderno, competitivo e centrado no usuário.

A organização em módulos temáticos facilita o planejamento por sprints e permite uma entrega incremental de valor, mantendo a essência ágil do projeto original. O mapeamento de requisitos por persona garante que as decisões de priorização sejam orientadas pela geração de valor real para os diferentes perfis de usuário.

Sugere-se que o desenvolvimento da versão 2.0 siga a seguinte ordem de prioridade de módulos: (1) Autenticação e Gestão de Conta, (2) Gestão de Vinhos com Scan, (3) Avaliação Rápida e Ficha de Degustação, (4) Sommelier Virtual com IA, (5) Dashboard e Insights, (6) Compartilhamento, e (7) Gamificação e Notificações.