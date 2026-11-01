# FinOps — Controle de Gastos

Teto do ciclo: **US$ 50** no projeto GCP do Alçadas, com alertas de orçamento em 50 % (US$ 25), 80 % (US$ 40) e 100 % (US$ 50). Metas por checkpoint (Caderno B2B v2):

| Checkpoint | Gasto acumulado máximo | Situação |
| :--- | :--- | :--- |
| CP2 — Núcleo funcional (01/11) | **US$ 5** | ⏳ aguardando print do faturamento |
| CP3 — Nuvem e integração (15/11) | US$ 12 | — |
| CP4 — Estabilização (29/11) | US$ 40 → acionar plano de contenção | — |

Até o CP2 tudo roda local, com `docker compose up` e emuladores do Firebase, então o gasto esperado é próximo de **US$ 0,00**.

---

## 1. Evidências do Checkpoint 2

Responsáveis: Tech Lead (com apoio de DevOps). Enviar os prints ao PO e salvá-los em `docs/assets/images/finops/`.

1. **Faturamento real** — Console do GCP → **Faturamento → Relatórios**, filtro pelo projeto do Alçadas, período desde 04/10/2026.
   Arquivo: `docs/assets/images/finops/cp2-faturamento.png`
2. **Alertas de orçamento** — **Faturamento → Orçamentos e alertas**, mostrando os limites de 50/80/100 %.
   Arquivo: `docs/assets/images/finops/cp2-orcamento-alertas.png`
3. **Estimativa** — [Calculadora de preços do Google Cloud](https://cloud.google.com/products/calculator) com Firestore + Cloud Run + Firebase Hosting no volume previsto para a demo.
   Arquivo: `docs/assets/images/finops/cp2-calculadora.png`

| Item | Valor | Data da leitura | Evidência |
| :--- | :--- | :--- | :--- |
| Gasto acumulado no projeto | _preencher com o valor do painel_ | _dd/mm_ | `cp2-faturamento.png` |
| Estimativa mensal da demo | _preencher com o valor da calculadora_ | _dd/mm_ | `cp2-calculadora.png` |

> Não preencher a tabela com valores estimados ou de memória: os números vêm do painel de faturamento.

---

## 2. Regras que mantêm o custo baixo

- Nenhum recurso novo no GCP sem ADR ou aprovação do Tech Lead ([DoD](dod.md)).
- Toda listagem paginada (`limit` ≤ 50) e toda consulta filtrada por `tenantId` ([OpenAPI v0.2](../api/index.md)).
- Nada de *listeners* grandes do Firestore no navegador — o Firestore é fechado para o cliente ([ADR-008](../arquitetura/adrs/ADR-008-firestore-deny-all.md)).
- Cloud Run com `min-instances=0` e imagens com SHA (Checkpoint 3).

---

## 3. Se o gasto passar da meta

Registrar em `docs/finops/estouro-<checkpoint>.md`: valor, causa, o que foi desligado e quando, e qual alerta disparou. Exemplo de formato: *"Gasto de US$ 6,20 por instância do Cloud Run esquecida ligada; desligada em 29/10; alerta de 50 % conferido."*

Plano de contenção a partir de US$ 40: reduzir instâncias, pausar o Cloud Scheduler e desligar recursos não críticos.
