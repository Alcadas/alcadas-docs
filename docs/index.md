# Alçadas

> **Sistema de Workflow de Alçadas de Aprovação e Rastreabilidade Operacional**  

![Alçadas Banner](assets/images/alcadas-banner.png)

Bem-vindo à documentação oficial do projeto **Alçadas**, uma plataforma *Cloud-Native* desenvolvida para estruturar, automatizar e blindar todo o fluxo de aprovações financeiras (compras, pagamentos e reembolsos) e governança corporativa.

---

## Visão Executiva

A **Startop LTDA** (prestadora de serviços em clínicas, franquias e construtoras de médio porte) enfrentava gargalos administrativos causados por aprovações informais via mensagens instantâneas, e-mails e planilhas desconectadas. O **Alçadas** substitui esse fluxo informal por um controle centralizado de limites financeiros, prevenção ativa de fraudes e uma trilha de auditoria criptograficamente verificável.

### Pilares Estratégicos

- **Auditoria Blindada e Imutável:** Trilha de auditoria baseada em selos digitais encadeados (**SHA-256**), garantindo detecção imediata (< 1 s) de qualquer tentativa de adulteração no banco de dados.
- **Motor Nativo de Riscos e Anti-Fracionamento:** Inteligência financeira integrada por Centro de Custo que bloqueia e escala preventivamente compras divididas em janela móvel de 30 dias e sinaliza duplicidades em janela de 7 dias.
- **Gestão de Fluxos Inteligente e Concorrente:** Máquina de estados transacional preparada para decisões concorrentes (`HTTP 409 Conflict`), solicitações de ajuste e delegações temporárias de competência sem ferir a Segregação de Funções (*SoD*).
- **Sincronização Operacional em Tempo Real:** Atualização instantânea (< 1,5 s) dos painéis de solicitantes, aprovadores e auditores sem necessidade de recarregar a página.

---

## As 5 Jornadas Principais do MVP

1. **Configuração de Limites e Centros de Custo:** Parametrização administrativa de tetos financeiros por perfil/cargo hierárquico (`RF01`, `US-01`).
2. **Submissão Guiada de Solicitações:** Registro formal de pedidos de compra, pagamento ou reembolso com validação dinâmica de justificativas (`RF02`, `US-02`).
3. **Avaliação, Ajustes e Delegação:** Decisão formal dentro da alçada (aprovar, rejeitar, pedir ajuste) e transferência temporária de competência em ausências (`RF03`, `RF04`, `RF10`, `RF11`).
4. **Sinalização Automática de Riscos:** Bloqueio preventivo de fracionamento (30 dias), alerta de duplicidade (7 dias) e aplicação estrita de Segregação de Funções (`RF05`, `RF06`, `RF12`).
5. **Auditoria e Conformidade:** Consulta da linha do tempo, recálculo de integridade da cadeia SHA-256 e exportação estruturada em CSV/JSON (`RF07`, `RF08`, `RF09`).

---

## Navegação da Documentação

| Seção | Conteúdo |
| :--- | :--- |
| **[Visão do Produto e Projeto](visao-produto/visao-produto.md)** | Cenário atual da Startop LTDA, Diagrama de Ishikawa (6M's), objetivos gerais e específicos, características da solução, análise competitiva, viabilidade (9 semanas / US$ 50 GCP) e impacto esperado. |
| **[Personas do Usuário](visao-produto/personas.md)** | Definição das 3 personas-alvo da Startop LTDA (Carlos, Mariana e Patrícia), mapas de empatia, dores, cenários de uso e matriz de rastreabilidade. |
| **[Requisitos do Sistema](requisitos.md)** | Especificação detalhada dos **12 Requisitos Funcionais (`RF01`–`RF12`)**, **12 Regras de Negócio (`RN01`–`RN12`)** e **10 Requisitos Não Funcionais (`RNF01`–`RNF10`)** classificados segundo Sommerville. |
| **[Histórias de Usuários](historias-usuario.md)** | Backlog completo com **22 Histórias de Usuários (`US-01`–`US-22`)** priorizadas pelo método **MoSCoW** (*Must, Should, Could, Won't*) e seus critérios de aceite testáveis. |
| **[Arquitetura](ARCHITECTURE.md)** | Arquitetura *Cloud-Native* (Angular, NestJS, Cloud Firestore, Firebase Auth e Docker em monorepo Nx), diagramas da máquina de estados, cadeia de *hashes* SHA-256 e conformidade LGPD. |

---

## Equipe & Mantenedores (UnB)

| Papel | Integrante |
| :--- | :--- |
| **Project Owner** | Matheus Ribeiro Szervinsk |
| **Tech Lead** | Henrique Schneider |
| **DevOps** | Pedro Henrique Gomes |
| **Backend** | Luis |
| **Frontend** | Ana Clara |
| **QA** | Leticia |
| **Cliente / Parceira** | Julia Campos Santana (*Startop LTDA*) |
