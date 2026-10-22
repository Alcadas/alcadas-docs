## 1. Cenário Atual do Cliente e do Negócio

### 1.1. Identificação do Cliente/Parceiro

| Atributo | Descrição |
| :--- | :--- |
| **Nome** | Startop LTDA |
| **Tipo** | Prestadora de serviços em clínicas, franquias e construtoras médias. *(empresa grande - big tech)* |
| **Representante** | Julia Campos Santana |
| **Forma de contato** | +55 61 9350-0062 |
| **Vínculo com o projeto** | Cliente real e parte interessada, responsável por validar o escopo, prioridades e entregas do sistema. |

---

### 1.2. Introdução ao Negócio e Contexto

A empresa **Startop LTDA** é uma prestadora de serviços independente que atua no segmento de clínicas, franquias e construtoras de médio porte. A organização possui uma rotina administrativa e operacional focada na prestação de serviços técnicos e terceirização. Atualmente, a gestão de solicitações de compra, pagamentos e reembolsos é conduzida por fluxos informais, aprovações verbais ou via mensagens instantâneas e planilhas desconectadas. Esse formato gera inconsistências nos registros, falta de rastreabilidade para auditorias, riscos de compras fracionadas não autorizadas e potenciais conflitos de interesse por ausência de segregação de funções. Diante desse cenário, surge a oportunidade de estruturar e blindar o fluxo de aprovações e governança financeira da empresa.

O fluxo operacional da Startop LTDA conecta solicitantes, gestores e o setor financeiro. A falta de um sistema centralizado de alçadas ocasiona ausência de verificação formal de limites financeiros, riscos de adulteração de registros e dificuldades no acompanhamento de aprovações em tempo real.

![Fluxo operacional informal da Startop LTDA e oportunidade de alçadas centralizadas](assets/images/fluxo-informal-startop.png)

---

### 1.3. Identificação da Oportunidade ou Problema

A Startop LTDA enfrenta gargalos administrativos decorrentes da falta de automação e controle nos processos de alçadas de aprovação. O crescimento das operações evidenciou problemas essenciais:

- **Aprovações Informais e Descentralizadas:** Solicitações realizadas e aprovadas via e-mail ou mensagens sem histórico consolidado.
- **Risco de Fracionamento e Duplicidade:** Ausência de travas automáticas para evitar a divisão de compras com o intuito de burlar limites de aprovação.
- **Opacidade na Trilha de Auditoria Total:** Dificuldade em consultar e exportar históricos confiáveis e verificáveis de aprovações e modificações.
- **Inexistência de Segregação de Funções:** Insegurança no cumprimento de regras de risco e sobreposição de papéis em solicitações financeiras.

Diante desse cenário, surge a oportunidade de desenvolver o **Alçadas**, uma plataforma para estruturar, automatizar e blindar todo o fluxo de aprovações financeiras da organização.

A seguir, apresenta-se o **Diagrama de Ishikawa** contendo as causas raízes (organizadas pelos 6M's) e o efeito principal na operação da Startop LTDA:

- **Método (Processos):** Fluxos de aprovação verbais, ausência de padronização e falta de regras claras de delegação em caso de férias.
- **Máquina (Tecnologia):** Uso de planilhas descentralizadas, trocas de e-mails e mensagens sem integração ou validação de limites.
- **Mão de Obra (Pessoas):** Falta de clareza da equipe sobre os tetos financeiros permitidos para cada cargo e sobrecarga dos diretores.
- **Medida (Controles):** Ausência de alertas para gastos duplicados ou compras fracionadas e inexistência de trilha de auditoria imutável.
- **Materiais (Insumos/Dados):** Dados financeiros espalhados, notas fiscais desencontradas e registros suscetíveis a edição manual.
- **Meio Ambiente (Contexto Externo):** Exigência crescente de conformidade (*compliance*) por parte das clínicas, franquias e construtoras parceiras.
- **Problema Principal (Efeito):** Fragilidade na governança financeira, risco de perdas por fracionamento/fraude e incapacidade de auditar aprovações com segurança.

![Diagrama de Ishikawa (6M's) — Fragilidade na governança financeira](assets/images/diagrama-ishikawa.png)

---

### 1.4. Desafio do Projeto

O principal desafio técnico do projeto é a construção de uma **máquina de estados robusta** que controle o ciclo de vida de cada solicitação financeira. O sistema precisará lidar com decisões simultâneas (evitando que dois gestores aprovem ou alterem o mesmo pedido ao mesmo tempo), gerenciar delegações temporárias de competência e garantir a integridade absoluta da trilha de auditoria por meio de *hashes* criptográficos encadeados, onde qualquer tentativa de adulterar um registro passado invalida a cadeia de verificação. No âmbito de produto, busca-se manter o escopo estritamente focado na governança de alçadas para assegurar a entrega de um MVP funcional dentro do cronograma de **9 semanas**.

---

### 1.5. Segmentação de Clientes

A solução da Startop LTDA atende a três perfis internos e operacionais distintos:

- **Gestores de Operações/Centros de Custo (Solicitantes):** Profissionais que atuam na ponta (administração de clínicas e obras) e precisam registrar rapidamente pedidos de compra, pagamentos de fornecedores ou reembolsos, visualizando com clareza em qual etapa de aprovação seu pedido se encontra.
- **Aprovadores e Diretores:** Executivos que necessitam de um painel simplificado e atualizado em tempo real para aprovar, rejeitar, pedir ajustes ou delegar solicitações dentro de suas respectivas faixas de valor (alçadas).
- **Auditores e Controladoria (Controller / CFO):** Profissionais que exigem consulta e exportação de trilhas encadeadas e verificáveis, identificando alertas de fracionamento, duplicidade ou conflito de funções.

---

## 2. Solução Proposta

### 2.1. Objetivo Geral do Produto

O objetivo geral do produto é reestruturar e blindar a governança dos fluxos operacionais e financeiros da Startop LTDA, mitigando aprovações informais, fracionamentos e falhas de auditoria. A solução fornece controle automatizado de alçadas configuráveis, gestão de delegação, regras de risco e trilha de auditoria verificável.

!!! info "As 5 Jornadas Principais do MVP"
    1. Configuração de limites financeiros e centros de custo.
    2. Criação de solicitações de compra, pagamento ou reembolso.
    3. Avaliação de solicitações (aprovação, rejeição, solicitação de ajustes e delegação).
    4. Sinalização automática de riscos (fracionamento, duplicidade e conflito de funções).
    5. Consulta, verificação de integridade e exportação da trilha de auditoria.

---

### 2.2. Objetivos Específicos

- **OE1 — Automação de Alçadas:** Estabelecer controle rigoroso de alçadas configuráveis e centros de custo, eliminando aprovações informais e garantindo a segregação de funções.
- **OE2 — Mitigação de Riscos Financeiros:** Implementar sinalização automática de fracionamento de compras, duplicidade de lançamentos e inconformidades de alçada.
- **OE3 — Rastreabilidade e Auditoria:** Assegurar a integridade da trilha de auditoria com *hashes* encadeados, permitindo detecção de registros adulterados e exportação para verificação.
- **OE4 — Gestão do Fluxo de Aprovação:** Viabilizar uma máquina de estados fluida para decisões concorrentes, tratativas de ajustes e delegação temporária de alçadas.

---

### 2.3. Características da Solução

A seguir, apresenta-se o conjunto inicial das principais características que o MVP da solução deverá contemplar:

- **Configuração de Centros de Custo e Faixas de Alçada:** Painel administrativo para definir tetos financeiros de aprovação exigidos para cada perfil de liderança.
- **Submissão Guiada de Solicitações:** Formulários inteligentes para pedidos de compra, pagamento ou reembolso que validam campos obrigatórios e exigem justificativas adicionais caso o valor ultrapasse limites pré-estabelecidos.
- **Motor de Alerta de Riscos (Anti-Fracionamento):** Verificação automática do histórico recente do centro de custo e do solicitante para alertar ou bloquear pedidos que, somados, ultrapassem o teto da alçada inferior.
- **Painel de Decisão e Delegação em Tempo Real:** Interface atualizada instantaneamente para que diretores avaliem pendências ou transfiram temporariamente seu poder de aprovação a um substituto durante ausências.
- **Módulo de Auditoria e Verificação de Integridade:** Tela dedicada para auditores consultarem a linha do tempo de cada pedido, validarem a integridade criptográfica dos logs e exportarem relatórios.
- **Segurança da Informação e Conformidade com a LGPD:** Em conformidade com a Lei Geral de Proteção de Dados (LGPD), o sistema armazena exclusivamente os dados pessoais de identificação corporativa dos colaboradores (nome completo, e-mail institucional, cargo/papel, ID de usuário e endereço IP de origem), com a finalidade de autenticar acessos, assegurar a segregação de funções e compor a trilha de auditoria financeira legal, mantendo esses registros pelo período de **5 (cinco) anos** após o encerramento do exercício fiscal para cumprimento de obrigações legais e de auditoria, sendo descartados ou anonimizados após esse prazo.

---

### 2.4. Tecnologias a Serem Utilizadas

A arquitetura do sistema Alçadas foi desenhada sob o paradigma *Cloud-Native*, priorizando alta disponibilidade, sincronização em tempo real e o rigor para suportar as regras de governança e auditoria. A *stack* tecnológica baseia-se em TypeScript/JavaScript de ponta a ponta, contêineres e serviços gerenciados em nuvem:

- **Frontend (Interface e Experiência do Usuário):** Será utilizado o **Angular**, framework desenvolvido em TypeScript. Sua adoção justifica-se pela capacidade nativa de gerenciar fluxos complexos de dados e formulários dinâmicos (*Reactive Forms*). Essa estrutura permite validar regras diretamente na tela e consumir atualizações do banco de dados, mantendo os painéis de diretores e solicitantes sincronizados em tempo real sem necessidade de recarregar a página.
- **Backend (API e Motor de Regras):** No desenvolvimento do motor de regras, a escolha é o **NestJS**, um framework focado na construção de aplicações escaláveis em arquitetura de camadas e também escrito em TypeScript. Ele será o responsável por centralizar e blindar as operações críticas que não podem ficar expostas no navegador, como o cálculo matemático anti-fracionamento, a máquina de estados para decisões simultâneas e a geração dos selos criptográficos (*hashes* encadeados) para a trilha de auditoria.
- **Persistência de Dados e Tempo Real:** A guarda de informações e a comunicação assíncrona serão gerenciadas pelo **Google Cloud Firestore**, um banco de dados NoSQL orientado a documentos operado na nuvem. A escolha baseia-se na sua alta escalabilidade e na capacidade de emitir atualizações nativas diretas para as telas. Dessa forma, toda vez que o backend registrar uma nova aprovação ou emitir um alerta de risco, as interfaces operacionais refletirão a mudança instantaneamente.
- **Autenticação e Gestão de Identidade:** O controle de identidades ficará a cargo do **Firebase Auth**, serviço dedicado à validação segura de credenciais e emissão de tokens JWT. Integrado aos mecanismos de proteção (*Guards*) do NestJS e do Angular, ele garantirá o Controle de Acesso Baseado em Papéis (RBAC) e a estrita segregação de funções. Isso garante que a pessoa que solicita uma compra não tenha permissão para aprová-la ou auditar o próprio registro.
- **Infraestrutura e Padronização de Ambientes:** O projeto adotará o **Docker** para conteinerização da aplicação. O Docker empacota o serviço principal em ambientes isolados e imutáveis, assegurando que o sistema execute de forma idêntica nas máquinas de desenvolvimento e em produção.

> Consulte a página de [Arquitetura](ARCHITECTURE.md) para detalhes técnicos aprofundados dos componentes e fluxos do sistema.

---

### 2.5. Pesquisa de Mercado e Análise Competitiva

O mercado de governança financeira, gestão de compras e fluxos de aprovação é maduro, porém historicamente fragmentado entre sistemas corporativos rígidos e ferramentas genéricas que exigem alto esforço de adequação.

Soluções consolidadas, como ERPs tradicionais (ex.: TOTVS), têm como ponto forte a centralização contábil e o controle fiscal. Contudo, sua estrutura engessada faz com que alterações em regras de alçada ou fluxos de delegação exijam customizações caras, demoradas e dependentes do fornecedor. Em contrapartida, produtos de BPM (*Business Process Management*), como o Pipefy, são altamente flexíveis e permitem o desenho visual de fluxos, mas pecam pela ausência de inteligência financeira nativa. A implementação de regras estritas — como a soma de notas recentes para evitar o fracionamento de compras — exige integrações complexas com outros sistemas, tornando a auditoria frágil e de difícil rastreabilidade.

O **Alçadas** posiciona-se como uma plataforma central de governança: leve, focada na experiência do usuário e rigorosa na segurança e integridade dos dados. A solução diferencia-se das ofertas de prateleira por meio de quatro pilares estratégicos e operacionais:

1. **Auditoria Blindada e Imutável:** Enquanto sistemas tradicionais armazenam históricos simples que podem ser alterados manualmente por administradores, o Alçadas implementa uma trilha de auditoria baseada em selos digitais encadeados. Cada evento no fluxo (aprovação, rejeição ou edição) gera um código único atrelado à transação anterior. Isso garante aos clientes da Startop LTDA um nível de conformidade pronto para auditorias externas rigorosas, padrão raramente acessível fora do setor bancário.
2. **Motor Nativo de Riscos e Anti-Fracionamento:** Plataformas genéricas desconhecem o conceito de "Centro de Custo" a menos que sejam extensivamente configuradas. O Alçadas possui inteligência financeira embutida. O sistema é capaz de intervir preventivamente, bloqueando uma solicitação de R$ 4.900,00 sob um teto de R$ 5.000,00 caso detecte gastos anteriores que configurem fracionamento para burlar a aprovação da diretoria.
3. **Gestão de Fluxos Inteligente e Concorrente:** Ao contrário de processos lineares geridos por e-mails ou planilhas — onde requisições ficam paralisadas durante as férias de um diretor —, o núcleo do sistema foi desenhado para lidar com múltiplos cenários simultâneos. Isso permite tratar de forma nativa delegações temporárias, aprovações parciais e devoluções para ajuste, eliminando gargalos operacionais sem ferir a regra de segregação de funções.
4. **Sincronização Operacional em Tempo Real:** Apoiado em tecnologias de nuvem modernas, os painéis de aprovação e operação permanecem conectados de forma contínua. Uma aprovação validada na matriz atualiza a tela da filial instantaneamente. Essa estrutura proporciona uma percepção de fluidez superior à dos softwares de gestão tradicionais, eliminando a necessidade de atualizar a página para acompanhar o andamento das solicitações.

!!! success "Hipótese de Defensibilidade e Métrica de Validação"
    A defensibilidade do Alçadas frente a concorrentes de workflow genéricos reside no acoplamento nativo entre a máquina de estados concorrente, o motor de regras de centro de custo e a cadeia criptográfica de *hashes* (SHA-256) no backend — uma arquitetura que plataformas *no-code* não conseguem replicar sem integrações externas lentas e vulneráveis a fraudes por administradores de banco. No **Demo Day**, essa defensibilidade será provada atingindo **100% de precisão e detecção em menos de 1 segundo** na identificação de registros adulterados diretamente no banco de dados e no bloqueio automático de compras fracionadas na massa de testes.

---

### 2.6. Análise de Viabilidade

- **Viabilidade Técnica:** Alta, pois a arquitetura adotada (Angular, NestJS, Firebase Auth e Firestore) utiliza TypeScript de ponta a ponta em um monorepo gerenciado pelo **Nx**, o que elimina falhas de contrato entre frontend e backend e acelera o desenvolvimento. Além disso, o uso de serviços gerenciados e emuladores locais do Firebase via Docker Compose permite desenvolver e testar toda a máquina de estados e a cadeia de *hashes* localmente, sem sobrecarga de infraestrutura.
- **Viabilidade de Prazo:** Plenamente exequível dentro do ciclo de **9 semanas**, estruturado em entregas incrementais:
    - **Até a Semana 2:** Estruturação, contratos e ambiente local;
    - **Até a Semana 4:** Jornadas principais, máquina de estados e regras de segurança;
    - **Até a Semana 6 e Demo Day:** Deploy em nuvem, observabilidade e validação final.
- **Viabilidade Financeira:** O projeto respeita rigorosamente o teto orçamentário de **US$ 50,00 por equipe** estabelecido para o ciclo na Google Cloud Platform (GCP). Como o desenvolvimento e os testes automatizados até a Semana 4 rodam integralmente em emuladores locais gratuitos (mantendo o gasto inicial abaixo de US$ 5,00), o custo total estimado de operação do Alçadas em nuvem situa-se entre **US$ 4,00 e US$ 10,00**, demonstrando excelente sustentabilidade financeira e aderência ao modelo de negócio da Startop LTDA.

---

### 2.7. Impacto da Solução

Espera-se que a implantação da plataforma Alçadas gere benefícios diretos e mensuráveis para a operação da Startop LTDA:

1. **Mitigação de Riscos e Proteção do Caixa:** O bloqueio e a sinalização automática de compras fracionadas ou duplicadas impedirão desvios de orçamento e gastos não autorizados, gerando economia financeira direta.
2. **Segurança Institucional em Auditorias:** A substituição de autorizações verbais por uma trilha de auditoria criptograficamente verificável eliminará questionamentos sobre a validade das aprovações, blindando a empresa em fiscalizações.
3. **Agilidade e Eliminação de Gargalos Operacionais:** A centralização dos pedidos em painéis em tempo real e a possibilidade de delegação formal evitarão que compras urgentes de clínicas ou obras fiquem paradas aguardando respostas de e-mail ou retorno de férias de diretores.
4. **Fortalecimento da Credibilidade no Mercado:** Operar sob um sistema rígido de *compliance* posiciona a Startop LTDA como uma parceira corporativa confiável, madura e transparente perante seus clientes de médio e grande porte.
5. **Conformidade com a LGPD e Governança de Dados:** O tratamento estrito dos dados pessoais corporativos dos colaboradores (nome, e-mail, cargo, ID e IP), coletados unicamente para fins de controle de acesso e responsabilização em trilha de auditoria e retidos pelo prazo legal de 5 anos antes da anonimização, assegura total aderência à legislação brasileira de privacidade e blinda a Startop LTDA contra passivos jurídicos.
