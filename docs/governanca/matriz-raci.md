# Matriz RACI de Responsabilidades

A matriz **RACI** define claramente as responsabilidades organizacionais ao longo do ciclo de vida das operações do sistema **Alçadas**. As letras representam:

- **R (Responsible / Responsável):** Quem executa a tarefa ou atividade operacional;
- **A (Accountable / Aprovador Final):** Quem responde pelo resultado perante a governança e tem poder de veto/decisão final;
- **C (Consulted / Consultado):** Especialistas ou partes consultadas antes ou durante a execução da atividade;
- **I (Informed / Informado):** Quem é comunicado automaticamente sobre a conclusão ou alteração de estado da atividade.

---

## 1. Tabela da Matriz RACI

| Atividade do Ciclo de Vida | Solicitante | Aprovador N1 (Coord.) | Aprovador N2 (Gerente) | Diretor / CFO (N3) | Auditor / Compliance | Administrador do Tenant |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Configuração de Centros de Custo e Tetos (`RF01`)** | I | I | C | A | C | **R** |
| **Submissão de Nova Solicitação de Despesa (`RF02`)** | **R** | I | I | I | I | - |
| **Reenvio de Solicitação em Ajuste (`RF10`)** | **R** | I | I | I | I | - |
| **Decisão de Pedido até R$ 1.000,00 (`RF03`)** | I | **R / A** | I | I | I | - |
| **Decisão de Pedido de R$ 1.000,01 a R$ 5.000,00 (`RF03`)** | I | - | **R / A** | I | I | - |
| **Decisão de Pedido > R$ 5.000,00 ou Escalonado (`RF03`)** | I | - | - | **R / A** | I | - |
| **Cadastro de Delegação Temporária (`RF04`)** | - | **R** | **R** | **R / A** | I | C |
| **Análise de Alertas de Fracionamento e Duplicidade (`RF05`, `RF06`)** | - | C | C | **R / A** | I | - |
| **Validação da Cadeia Criptográfica SHA-256 (`RF08`)** | - | - | - | I | **R / A** | C |
| **Exportação de Relatórios de Conformidade (`RF09`)** | - | - | I | I | **R / A** | I |
| **Gestão de Usuários e Atribuição de Perfis RBAC** | - | C | C | A | C | **R** |

---

## 2. Princípios de Governança Aplicados

1. **Unicidade de Autoridade Final (Accountability):** Para qualquer solicitação individual, existe exatamente **um único nível aprovador final (A)** ativo para uma determinada transação de estado, eliminando ambiguidade hierárquica.
2. **Segregação Estrita de Atores:** Em nenhuma hipótese o perfil que detém o papel **R** na submissão de um pedido acumulará a letra **A** na aprovação desse mesmo item (*Regra de Segregação de Funções — RN03 / RN12*).
3. **Auditoria Independente e Isenta:** O perfil de Auditoria atua exclusivamente com privilégios de leitura e verificação matemática da cadeia de hashes (`RF08`), não participando da cadeia decisória (**R** ou **A**) de compras ou pagamentos.
