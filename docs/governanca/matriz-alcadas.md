# Matriz de Alçadas e Competências Financeiras

A **Matriz de Alçadas** define a autoridade decisória conferida a cada nível hierárquico dentro de uma organização ou clínica cadastrada na plataforma **Alçadas**. O objetivo é blindar o fluxo de compras, garantindo que compromissos financeiros respeitem limites orçamentários rígidos (`RF01`, `RN01`).

---

## 1. Faixas de Limites Financeiros e Níveis de Autoridade

Cada organização parametriza os seus tetos por centro de custo. A tabela abaixo reflete a parametrização padrão acordada para o cenário da **Startop LTDA**:

| Nível de Alçada | Papel / Cargo Equivalente | Limite Financeiro Individual | Categorias Autorizadas | Mecanismo de Decisão |
| :--- | :--- | :--- | :--- | :--- |
| **Nível 1 (N1)** | Coordenador / Supervisor de Setor | Até **R$ 1.000,00** | Material de Escritório, Reembolsos Operacionais, Insumos Rápidos. | Aprovação monocrática de 1 aprovador de N1. |
| **Nível 2 (N2)** | Gerente de Departamento | De **R$ 1.000,01** até **R$ 5.000,00** | Serviços de Terceiros, Softwares, Treinamentos, Manutenção Predial. | Aprovação de 1 gestor com papel de N2. |
| **Nível 3 (N3)** | Diretoria Executiva / CFO | Acima de **R$ 5.000,00** *(Sem limite superior)* | Contratos de Médio/Longo Prazo, CAPEX, Aquisição de Equipamentos. | Aprovação do Diretor da Área ou CFO. |

---

## 2. Regras de Escalonamento e Travas do Sistema

### 2.1. Trava Anti-Fracionamento (`RF05` / `RN05`)
- Se um solicitante registrar múltiplos pedidos abaixo de R$ 1.000,00 para o mesmo centro de custo e categoria em uma **janela móvel de 30 dias**, o sistema soma as solicitações acumuladas.
- **Gatilho de Escalonamento:** Se o valor acumulado ultrapassar o teto do nível corrente (ex.: soma de R$ 1.250,00), o pedido é automaticamente escalonado para o **Nível 2 (Gerência)**, retirando a competência do N1 e registrando o motivo no histórico.

### 2.2. Sinalização de Duplicidade (`RF06` / `RN06`)
- Pedidos com o mesmo **valor monetário**, mesmo **centro de custo** e mesmo **fornecedor** emitidos em um intervalo inferior a **7 dias corridos** acionam o selo visual de alerta:
  > **[Alerta de Risco]** `Possível duplicidade identificada com a solicitação #REQ-XXXXX submetida há 3 dias.`

### 2.3. Bloqueio Inegociável de Autoaprovação (*SoD* — `RN03`, `RN12`)
- Nenhum usuário pode aprovar pedidos criados por si mesmo, independentemente de possuir o papel de Aprovador N1, N2 ou Diretor Executivo.
- Caso um Diretor submeta uma despesa própria, a solicitação é redirecionada compulsoriamente para outro Diretor ou para o CFO.

---

## 3. Gestão de Delegações Temporárias (`RF04` / `RN04`)

Para evitar gargalos operacionais durante períodos de férias, viagens ou licenças de diretores:

1. **Configuração Explícita:** O titular deve cadastrar o substituto, a data/hora inicial e a data/hora final da vigência.
2. **Registro Imutável:** A criação da delegação gera imediatamente um elo criptográfico na trilha com o identificador do titular e do substituto.
3. **Expiração Automática:** Ao expirar o horário estipulado, o sistema revoga imediatamente a competência delegada sem intervenção humana.
4. **Preservação de SoD (`RN12`):** Se o aprovador substituto for o autor de alguma despesa pendente, o sistema impede a aprovação mesmo que sua delegação de diretor esteja ativa.
