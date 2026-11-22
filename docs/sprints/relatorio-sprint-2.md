# Relatório da Sprint 2 — Núcleo Funcional e Regras de Segurança

| Atributo | Descrição |
| :--- | :--- |
| **Sprint** | Sprint 2 (Semanas S3 e S4) |
| **Período** | 19/10/2026 a 01/11/2026 (PRs mergeados de 23/10 a 02/11) |
| **Checkpoint** | Checkpoint 2 — Núcleo Funcional (gate em 01/11/2026) |
| **Cliente/Parceiro** | Startop LTDA — Julia Campos Santana |
| **Responsável pelo relatório** | Henrique Schneider (Tech Lead) |
| **Data de emissão** | 22/11/2026 |

---

## 1. Objetivo da Sprint

Validar o fluxo ponta a ponta das jornadas do MVP rodando localmente: configuração de centros de custo e tetos, submissão de solicitações e decisão do aprovador, com máquina de estados, SoD e Security Rules *deny-all*, conforme o [Cronograma](../visao-produto/cronograma.md#sprint-2-nucleo-funcional-e-regras-de-seguranca-1910-a-01112026).

---

## 2. Resumo Executivo

A sprint concentrou o desenvolvimento das jornadas 1 a 3. O backend recebeu centros de custo e tetos (`alcadas-core#14`), submissão de solicitação (`#18`) e decisão do aprovador (`#15`). O frontend recebeu login, dashboard, telas de solicitações, alçadas, aprovações, regras, delegações, perfis e configurações da empresa. **10 dos 13 PRs foram mergeados nos dois últimos dias** (31/10 a 02/11). Do lado da documentação, o contrato OpenAPI v0.2, as ADR-008 a ADR-011 e a DoD completa ficaram prontos em 01/11 (`alcadas-docs#17`), mas o PR só foi mergeado em 14/11. As 4 issues do CP2 no `alcadas-core` foram fechadas em 05/11; as 4 do `alcadas-docs` seguem abertas. A decisão mais importante da sprint foi o **Plano Custo Zero** ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)): sem o voucher do GCP, a API vai para o Render e o Firebase fica no plano Spark.

| Indicador | Valor |
| :--- | :-: |
| Issues do Checkpoint 2 | 8 planejadas · 4 concluídas (todas em 05/11) |
| Pull Requests mergeados | 13 no `alcadas-core` (+ `alcadas-docs#17`, aberto em 01/11 e mergeado em 14/11) |
| PRs com aprovação de outra pessoa | 12 de 13 |
| Gasto em nuvem | US$ 0,00 (só emuladores) |
| Resultado do Checkpoint 2 | _a preencher pela equipe_ |

---

## 3. Entregas do Checkpoint 2 por Papel

Legenda de status: ✅ Concluído · 🟡 Parcial · ⏳ Pendente · ➡️ Entregue em sprint posterior

### 3.1. PO — Matheus Ribeiro Szervinsk

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Telas do frontend: login, menu lateral e dashboard | ✅ | `alcadas-core#12` | |
| Catálogo de regras, gerenciamento de alçadas e editor de fluxo visual | ✅ | `alcadas-core#13` | |
| Tela de solicitações e design system | ✅ | `alcadas-core#17` | |
| Telas completas de alçadas, aprovações, regras e delegações | ✅ | `alcadas-core#22` | |
| Redesenho de perfil, configurações e organização | ✅ | `alcadas-core#24` | Mergeado sem aprovação |
| Issue de documentação do PO (`alcadas-docs#16`) | ⏳ | — | Aberta |

### 3.2. Tech Lead — Henrique Schneider

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Contrato OpenAPI v0.2.0 e convenções | ➡️ | [api/index.md](../api/index.md), [ADR-009](../arquitetura/adrs/ADR-009-contrato-api-v0-2.md) | `alcadas-docs#17`, mergeado em 14/11 |
| Firestore fechado para o navegador | ➡️ | [ADR-008](../arquitetura/adrs/ADR-008-firestore-deny-all.md) | Mesmo PR |
| Concorrência otimista | ➡️ | [ADR-010](../arquitetura/adrs/ADR-010-concorrencia-otimista-rest.md) | Mesmo PR |
| Plano Custo Zero e FinOps | ➡️ | [ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md), [FinOps](../finops/index.md) | Mesmo PR; depende do aval do professor |
| DoD completa, CODEOWNERS e template de PR com a DoD | ➡️ | [dod.md](../governanca/dod.md), [Regras do repositório](../governanca/regras-repositorio.md) | Mesmo PR |
| Entregas do Tech Lead no código | ✅ | `alcadas-core#20` | Issue `alcadas-core#11` |

### 3.3. DevOps — Pedro Henrique Gomes

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| API de configuração de centros de custo e tetos (Jornada 1) | ✅ | `alcadas-core#14` | |
| API de decisão do aprovador (Jornada 3) | ✅ | `alcadas-core#15` | Issue `alcadas-core#10` |
| Entregas de DevOps do CP2 | ➡️ | `alcadas-core#64` | Mergeado na `main` em 05/11 |

### 3.4. QA — Leticia

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Tela de profissões e perfis em Delegações & SoD | ✅ | `alcadas-core#19` | |
| Testes de contrato contra o OpenAPI | ➡️ | `alcadas-core#93` | 19/11 |
| Testes de autorização das jornadas 1 a 3 | ➡️ | `alcadas-core#94` | 20/11 |
| Suíte EP/BVA v1 | ➡️ | `alcadas-core#95` | 20/11 |
| Matriz de rastreabilidade CA → teste → resultado | ➡️ | [rastreabilidade.md](../rastreabilidade.md) | `alcadas-docs#24`, 22/11 |

### 3.5. Front/Back — Ana Clara e Luis

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Telas de cadastro e recuperação | ✅ | `alcadas-core#16` | |
| Sidebar de perfil, configurações da empresa e segurança | ✅ | `alcadas-core#21` | |
| API de submissão de solicitação (Jornada 2) | ✅ | `alcadas-core#18` | |

---

## 4. Histórico de Pull Requests

| Repositório | PR | Merge | Descrição | Autor | Revisor |
| :--- | :-: | :-: | :--- | :--- | :--- |
| core | [#12](https://github.com/Alcadas/alcadas-core/pull/12) | 31/10 | Login, menu lateral e dashboard | Szervinsk | phenric26 |
| core | [#13](https://github.com/Alcadas/alcadas-core/pull/13) | 31/10 | Regras de negócio, alçadas e editor de fluxo | Szervinsk | phenric26 |
| core | [#15](https://github.com/Alcadas/alcadas-core/pull/15) | 31/10 | API de decisão do aprovador | phenric26 | Szervinsk |
| core | [#17](https://github.com/Alcadas/alcadas-core/pull/17) | 31/10 | Tela de solicitações e design system | Szervinsk | phenric26 |
| core | [#16](https://github.com/Alcadas/alcadas-core/pull/16) | 01/11 | Telas de cadastro e recuperação | anacllaraxy | phenric26 |
| core | [#19](https://github.com/Alcadas/alcadas-core/pull/19) | 01/11 | Profissões e perfis em Delegações & SoD | leticiadsp | Szervinsk |
| core | [#14](https://github.com/Alcadas/alcadas-core/pull/14) | 01/11 | API de centros de custo e tetos | phenric26 | Szervinsk |
| core | [#18](https://github.com/Alcadas/alcadas-core/pull/18) | 01/11 | API de submissão de solicitação | LuizFelipe81 | phenric26 |
| core | [#20](https://github.com/Alcadas/alcadas-core/pull/20) | 01/11 | Entregas do Tech Lead no CP2 | SchneiderCode1 | Szervinsk, phenric26 |
| core | [#22](https://github.com/Alcadas/alcadas-core/pull/22) | 01/11 | Telas de alçadas, aprovações, regras e delegações | Szervinsk | anacllaraxy |
| core | [#21](https://github.com/Alcadas/alcadas-core/pull/21) | 02/11 | Perfil, configurações da empresa e segurança | anacllaraxy | Szervinsk |
| core | [#24](https://github.com/Alcadas/alcadas-core/pull/24) | 02/11 | Redesenho de perfil, configurações e organização | Szervinsk | — |
| core | [#23](https://github.com/Alcadas/alcadas-core/pull/23) | 02/11 | Sincronização da `develop` | SchneiderCode1 | Szervinsk |
| docs | [#17](https://github.com/Alcadas/alcadas-docs/pull/17) | 14/11 | OpenAPI v0.2, ADR-008 a ADR-011, DoD e FinOps | SchneiderCode1 | phenric26 |

---

## 5. Reuniões da Sprint

| Data | Reunião | Principais decisões | Ata |
| :-: | :--- | :--- | :--- |
| 19/10/2026 | Planejamento da Sprint 2 e review do Gate 1 | _a preencher pela equipe_ | _a preencher pela equipe_ |
| 26/10/2026 | Acompanhamento S4 e preparação do Gate 2 | _a preencher pela equipe_ | _a preencher pela equipe_ |
| 01/11/2026 | Apresentação do Checkpoint 2 | _a preencher pela equipe_ | _a preencher pela equipe_ |

---

## 6. Controle de Custos (FinOps)

| Indicador | Valor |
| :--- | :-: |
| Meta do Caderno no CP2 | Gasto ≤ US$ 5,00 |
| Meta adotada | US$ 0,00 + decisão registrada em ADR ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)) |
| Gasto na Sprint 2 | US$ 0,00 (desenvolvimento só em emuladores) |
| Evidência | ⏳ prints dos planos gratuitos, ver [FinOps](../finops/index.md) |

---

## 7. Pendências e Impedimentos

| Item | Responsável | Impacto | Ação / Prazo |
| :--- | :--- | :--- | :--- |
| 10 de 13 PRs mergeados nos dois últimos dias | Toda a equipe | Revisão apressada; integração ficou para a Sprint 3 (`alcadas-core#66`) | Corte de PRs na quinta-feira antes do gate |
| PR da documentação do CP2 (`alcadas-docs#17`) aberto até 14/11 | Tech Lead | ADR-011 e OpenAPI v0.2 fora da `main` no gate | Mergeado em 14/11 |
| Testes de QA do CP2 (contrato, autorização, EP/BVA) | QA | Critérios do gate sem evidência automatizada em 01/11 | Entregues em 19 e 20/11 (`alcadas-core#93` a `#98`) |
| Aval do professor para as exceções da ADR-011 | Tech Lead | Pode reprovar o CP3 | Ver [Sprint 3](relatorio-sprint-3.md) |
| Dívidas técnicas abertas em 05/11 (`alcadas-core#65` a `#69`) | Equipe | Integração ponta a ponta, recuperação de senha, construtor de regras e tutoriais | Tratadas na Sprint 3 |

---

## 8. Riscos Identificados

| Risco | Probabilidade | Impacto | Mitigação |
| :--- | :-: | :-: | :--- |
| Sem voucher do GCP, o deploy previsto no Caderno é inviável | Certa | Alto | Plano Custo Zero ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)) |
| Exceções ao Caderno não aceitas pelo professor | Média | Alto | Pedir o aval como primeira atividade do CP3 (`alcadas-docs#19`) |
| Concorrência otimista via REST, sem transação real | Média | Médio | [ADR-010](../arquitetura/adrs/ADR-010-concorrencia-otimista-rest.md); migração para o Admin SDK no CP3 |

---

## 9. Feedback do Checkpoint 2

- **Tutor/Banca:** _a preencher pela equipe_
- **Cliente (Startop LTDA):** _a preencher pela equipe_

---

## 10. Retrospectiva

| O que funcionou bem | O que pode melhorar | Ações para a Sprint 3 |
| :--- | :--- | :--- |
| _a preencher pela equipe_ | _a preencher pela equipe_ | _a preencher pela equipe_ |

---

## 11. Próximos Passos — Sprint 3

Foco da [Sprint 3](../visao-produto/cronograma.md#sprint-3-nuvem-integracao-continua-e-observabilidade-0211-a-15112026): nuvem, integração contínua e observabilidade (02/11 a 15/11/2026).

- [x] Integração ponta a ponta de fluxos, solicitações e aprovações (`alcadas-core#66`)
- [x] API no Firestore real via Admin SDK, com `/readyz` (`alcadas-core#29`)
- [ ] Publicação com CI/CD (`alcadas-core#48`) — ver [Sprint 3](relatorio-sprint-3.md)
