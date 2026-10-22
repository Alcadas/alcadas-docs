# Relatório da Sprint 1 — Fundação e Arquitetura Base

| Atributo | Descrição |
| :--- | :--- |
| **Sprint** | Sprint 1 (Semanas S1 e S2) |
| **Período** | 05/10/2026 a 18/10/2026 |
| **Checkpoint** | Checkpoint 1 — Fundação (gate em 18/10/2026) |
| **Cliente/Parceiro** | Startop LTDA — Julia Campos Santana |
| **Responsável pelo relatório** | Henrique Schneider |
| **Data de emissão** | 22/10/2026 |

---

## 1. Objetivo da Sprint

Validar o problema e o escopo do Alçadas, definir a arquitetura base, preparar o ambiente emulado e estabelecer a governança da equipe, conforme o [Cronograma](../visao-produto/cronograma.md#sprint-1-fundacao-e-arquitetura-base-0510-a-18102026).

---

## 2. Resumo Executivo

<!-- 1 parágrafo: o que a equipe entregou, o que ficou pendente e o resultado do gate. -->

_A preencher._

| Indicador | Valor |
| :--- | :-: |
| Issues planejadas | _7_ |
| Issues concluídas | _7_ |
| Pull Requests mergeados | _6_ |
| Gasto GCP acumulado | US$ _–_ |
| Resultado do Checkpoint 1 | _Aprovado (com ressalvas)_ |

---

## 3. Entregas do Checkpoint 1 por Papel

Legenda de status: ✅ Concluído · 🟡 Parcial · ⏳ Pendente

### 3.1. PO — Matheus Ribeiro Szervinsk

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Visão do Produto | ✅ Concluído | [visao-produto.md](../visao-produto/visao-produto.md) | |
| Diagrama de Ishikawa (6M) | ✅ Concluído | [visao-produto.md#13](../visao-produto/visao-produto.md#13-identificacao-da-oportunidade-ou-problema) | |
| Backlog com MoSCoW | ✅ Concluído | [historias-usuario.md](../historias-usuario.md) | |
| Critérios de aceite mensuráveis | ✅ Concluído | [historias-usuario.md](../historias-usuario.md) | Apoio: QA |
| Entrevistas de validação (2) | ⏳ Pendente | _A preencher_ | |
| Hipótese de defensibilidade | ✅ Concluído | [visao-produto.md](../visao-produto/visao-produto.md) |  |
| Apresentação do gate | ⏳ Pendente | _A preencher_ |  |

### 3.2. Tech Lead — Henrique Schneider

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| ADR-001 a ADR-004 | ✅ Concluído | [Catálogo de ADRs](../arquitetura/adrs/index.md) | |
| ADR — Máquina de estados | ✅ Concluído | [ADR-005](../arquitetura/adrs/ADR-005-maquina-de-estados.md) | |
| ADR — Cadeia de hash | ✅ Concluído | [ADR-006](../arquitetura/adrs/ADR-006-cadeia-de-hash.md) | |
| ADR — RBAC | ✅ Concluído | [ADR-007](../arquitetura/adrs/ADR-007-rbac-e-sod.md) | |
| Tabela de transições | ✅ Concluído | [tabela-transicoes.md](../arquitetura/tabela-transicoes.md) | Apoio: PO |
| OpenAPI v0.1 | ✅ Concluído | [api/index.md](../api/index.md) | Apoio: Front/Back |
| Definition of Done | ✅ Concluído | [dod.md](../governanca/dod.md) | |

### 3.3. DevOps — Pedro Henrique Gomes

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Regras do repositório (branch protegida, CODEOWNERS, template de PR) | ✅ Concluído | [Commit 75be813](https://github.com/Alcadas/alcadas-core/commit/75be813a3393ab5e5ba4922e77dc156f3c4f582f) |  |
| `.env.example` sem segredos | ✅ Concluído | [Commit 04ebb5a](https://github.com/Alcadas/alcadas-core/commit/04ebb5a0752e24a694870993db1a93c797d924c9) | |
| Docker Compose com emuladores e seed | ✅ Concluído | [Commit dc904b0](https://github.com/Alcadas/alcadas-core/commit/dc904b0327ce3bc50639a56a4c68e56b5dad96e6) | |
| CI com lint e build | ✅ Concluído | [Commit 04ebb5a](https://github.com/Alcadas/alcadas-core/commit/04ebb5a0752e24a694870993db1a93c797d924c9) | |
| Alertas de orçamento (50/80/100%) | | _A preencher (print do console)_ | |

### 3.4. QA — Leticia

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Estratégia de testes | ✅ Concluído | [estrategia_de_testes.md](../testes/estrategia_de_testes.md) | Apoio: Tech Lead |
| Teste de isolamento de tenant | ✅ Concluído | [Commit ac48b02](https://github.com/Alcadas/alcadas-core/commit/ac48b02dec6a96e631acf17d56fb8bc24122ac45) | |

### 3.5. Front/Back — Ana Clara e Luis

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Esqueleto do frontend e da API | ✅ Concluído | [Commit 672732c](https://github.com/Alcadas/alcadas-core/commit/672732c806603e071da57b6846ebdd78ab6e8afd) [Commit e210dee](https://github.com/Alcadas/alcadas-core/commit/e210dee2e03734a93b5bcc408da695b536c1f453) | |

### 3.6. Entregas extras (fora do escopo mínimo do gate)

| Item | Evidência |
| :--- | :--- |
| Personas do usuário | [personas.md](../visao-produto/personas.md) |
| Cronograma da equipe | [cronograma.md](../visao-produto/cronograma.md) |
| Requisitos funcionais e não funcionais | [requisitos.md](../requisitos.md) |
| Visão geral da arquitetura | [ARCHITECTURE.md](../ARCHITECTURE.md) |
| Site de documentação (MkDocs) | [alcadas.github.io/alcadas-docs/](alcadas.github.io/alcadas-docs/) |

---

## 4. Histórico de Pull Requests

<!-- Complemente com os PRs dos demais repositórios do projeto. -->

| PR | Data do merge | Descrição | Autor | Revisor |
| :-: | :-: | :--- | :--- | :--- |
| #7 | 17/10/2026 | Estrutura do MkDocs e workflows de CI/CD | | |
| #8 | 18/10/2026 | Visão do produto, requisitos e histórias de usuário | | |
| #9 | 18/10/2026 | Ajuste do guia de contribuição | | |
| #10 | 20/10/2026 | Estratégia de testes | | |
| #11 | 20/10/2026 | ADRs, máquina de estados, tabela de transições, OpenAPI v0.1 e DoD | | |
| #12 | 21/10/2026 | Personas e cronograma | | |

---

## 5. Reuniões da Sprint

| Data | Reunião | Principais decisões | Ata |
| :-: | :--- | :--- | :--- |
| 05/10/2026 | Kickoff e planejamento inicial | _A preencher_ | _A preencher_ |
| 12/10/2026 | Sincronização S2 e preparação do gate | _A preencher_ | _A preencher_ |
| 18/10/2026 | Apresentação do Checkpoint 1 | _A preencher_ | _A preencher_ |

---

## 6. Controle de Custos (FinOps)

| Indicador | Valor |
| :--- | :-: |
| Teto do projeto | US$ 50,00 |
| Gasto na Sprint 1 | US$ _–_ |
| Gasto acumulado | US$ _–_ |
| Alertas configurados | _Sim / Não_ |

---

## 7. Pendências e Impedimentos

| Item | Responsável | Impacto | Ação / Prazo |
| :--- | :--- | :--- | :--- |
| _A preencher_ | | | |

---

## 8. Riscos Identificados

| Risco | Probabilidade | Impacto | Mitigação |
| :--- | :-: | :-: | :--- |
| _A preencher_ | | | |

---

## 9. Feedback do Checkpoint 1

<!-- Comentários do tutor/banca e do cliente sobre o gate. -->

- **Tutor/Banca:** _A preencher_
- **Cliente (Startop LTDA):** _A preencher_

---

## 10. Retrospectiva

| O que funcionou bem | O que pode melhorar | Ações para a Sprint 2 |
| :--- | :--- | :--- |
| _A preencher_ | _A preencher_ | _A preencher_ |

---

## 11. Próximos Passos — Sprint 2

Foco da [Sprint 2](../visao-produto/cronograma.md#sprint-2-nucleo-funcional-e-regras-de-seguranca-1910-a-01112026): núcleo funcional e regras de segurança (19/10 a 01/11/2026).

- [ ] _A preencher_