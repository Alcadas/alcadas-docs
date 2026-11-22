# Relatório da Sprint 1 — Fundação e Arquitetura Base

| Atributo | Descrição |
| :--- | :--- |
| **Sprint** | Sprint 1 (Semanas S1 e S2) |
| **Período** | 05/10/2026 a 18/10/2026 |
| **Checkpoint** | Checkpoint 1 — Fundação (gate em 18/10/2026) |
| **Cliente/Parceiro** | Startop LTDA — Julia Campos Santana |
| **Responsável pelo relatório** | Henrique Schneider (Tech Lead) |
| **Data de emissão** | 22/10/2026 · atualizado em 22/11/2026 |

---

## 1. Objetivo da Sprint

Validar o problema e o escopo do Alçadas, definir a arquitetura base, preparar o ambiente emulado e estabelecer a governança da equipe, conforme o [Cronograma](../visao-produto/cronograma.md#sprint-1-fundacao-e-arquitetura-base-0510-a-18102026).

---

## 2. Resumo Executivo

A equipe fechou as 6 issues do Checkpoint 1 no `alcadas-docs`, uma por papel mais a estrutura do MkDocs, e mergeou 13 Pull Requests nos dois repositórios. A fundação ficou documentada: visão do produto com Ishikawa, backlog MoSCoW, ADR-001 a ADR-007, tabela de transições, OpenAPI v0.1, DoD e estratégia de testes. O `alcadas-core` ganhou o monorepo Nx com esqueleto do frontend e da API, Docker Compose com emuladores e seed, CI com lint e build e o primeiro teste de isolamento de tenant. Parte das entregas foi mergeada entre 20 e 22/10, depois da data do gate. **9 dos 13 PRs foram mergeados sem aprovação registrada**, o que a DoD passou a proibir a partir do CP2.

| Indicador | Valor |
| :--- | :-: |
| Issues do Checkpoint 1 | 6 planejadas · 6 concluídas |
| Pull Requests mergeados | 13 (6 no `alcadas-docs`, 7 no `alcadas-core`) |
| PRs com aprovação de outra pessoa | 4 de 13 |
| Gasto em nuvem | US$ 0,00 (só emuladores) |
| Resultado do Checkpoint 1 | _a preencher pela equipe_ |

---

## 3. Entregas do Checkpoint 1 por Papel

Legenda de status: ✅ Concluído · 🟡 Parcial · ⏳ Pendente · 🔁 Substituído

### 3.1. PO — Matheus Ribeiro Szervinsk

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Visão do Produto | ✅ | [visao-produto.md](../visao-produto/visao-produto.md) | `alcadas-docs#8` |
| Diagrama de Ishikawa (6M) | ✅ | [visao-produto.md#13](../visao-produto/visao-produto.md#13-identificacao-da-oportunidade-ou-problema) | |
| Backlog com MoSCoW | ✅ | [historias-usuario.md](../historias-usuario.md) | |
| Critérios de aceite mensuráveis | ✅ | [historias-usuario.md](../historias-usuario.md) | Apoio: QA |
| Entrevistas de validação (2) | ⏳ | _a preencher pela equipe_ | Sem registro no repositório |
| Hipótese de defensibilidade | ✅ | [visao-produto.md](../visao-produto/visao-produto.md) | |
| Apresentação do gate | ⏳ | _a preencher pela equipe_ | Sem registro no repositório |

### 3.2. Tech Lead — Henrique Schneider

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| ADR-001 a ADR-004 | ✅ | [Catálogo de ADRs](../arquitetura/adrs/index.md) | `alcadas-docs#11` |
| ADR — Máquina de estados | ✅ | [ADR-005](../arquitetura/adrs/ADR-005-maquina-de-estados.md) | |
| ADR — Cadeia de hash | ✅ | [ADR-006](../arquitetura/adrs/ADR-006-cadeia-de-hash.md) | |
| ADR — RBAC | ✅ | [ADR-007](../arquitetura/adrs/ADR-007-rbac-e-sod.md) | |
| Tabela de transições | ✅ | [tabela-transicoes.md](../arquitetura/tabela-transicoes.md) | Apoio: PO |
| OpenAPI v0.1 | ✅ | [api/index.md](../api/index.md) | Apoio: Front/Back. Hoje na v0.3 |
| Definition of Done | ✅ | [dod.md](../governanca/dod.md) | Completada no CP2 |

### 3.3. DevOps — Pedro Henrique Gomes

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Regras do repositório (branch protegida, CODEOWNERS, template de PR) | ✅ | [Commit 75be813](https://github.com/Alcadas/alcadas-core/commit/75be813a3393ab5e5ba4922e77dc156f3c4f582f) | |
| `.env.example` sem segredos | ✅ | [Commit 04ebb5a](https://github.com/Alcadas/alcadas-core/commit/04ebb5a0752e24a694870993db1a93c797d924c9) | |
| Docker Compose com emuladores e seed | ✅ | [Commit dc904b0](https://github.com/Alcadas/alcadas-core/commit/dc904b0327ce3bc50639a56a4c68e56b5dad96e6) | Corrigido em `alcadas-core#5` e `#6` (21 e 22/10) |
| CI com lint e build | ✅ | [Commit 04ebb5a](https://github.com/Alcadas/alcadas-core/commit/04ebb5a0752e24a694870993db1a93c797d924c9) | |
| Alertas de orçamento (50/80/100 %) | 🔁 | [ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md) | Sem conta de faturamento não há alertas; substituídos por prints dos planos gratuitos ([FinOps](../finops/index.md)) |

### 3.4. QA — Leticia

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Estratégia de testes | ✅ | [estrategia_de_testes.md](../testes/estrategia_de_testes.md) | `alcadas-docs#10`. Apoio: Tech Lead |
| Teste de isolamento de tenant | ✅ | [Commit ac48b02](https://github.com/Alcadas/alcadas-core/commit/ac48b02dec6a96e631acf17d56fb8bc24122ac45) | `alcadas-core#3`, ainda marcado como pendente no teste |

### 3.5. Front/Back — Ana Clara e Luis

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Esqueleto do frontend e da API | ✅ | [Commit 672732c](https://github.com/Alcadas/alcadas-core/commit/672732c806603e071da57b6846ebdd78ab6e8afd) [Commit e210dee](https://github.com/Alcadas/alcadas-core/commit/e210dee2e03734a93b5bcc408da695b536c1f453) | `alcadas-core#4` |

### 3.6. Entregas extras (fora do escopo mínimo do gate)

| Item | Evidência |
| :--- | :--- |
| Personas do usuário | [personas.md](../visao-produto/personas.md) (`alcadas-docs#12`) |
| Cronograma da equipe | [cronograma.md](../visao-produto/cronograma.md) (`alcadas-docs#12`) |
| Requisitos funcionais e não funcionais | [requisitos.md](../requisitos.md) |
| Visão geral da arquitetura | [ARCHITECTURE.md](../ARCHITECTURE.md) |
| Site de documentação (MkDocs) | [alcadas.github.io/alcadas-docs](https://alcadas.github.io/alcadas-docs/) (`alcadas-docs#7`) |

---

## 4. Histórico de Pull Requests

| Repositório | PR | Merge | Descrição | Autor | Revisor |
| :--- | :-: | :-: | :--- | :--- | :--- |
| docs | [#7](https://github.com/Alcadas/alcadas-docs/pull/7) | 18/10 | Estrutura do MkDocs e workflows de CI/CD | Szervinsk | — |
| docs | [#8](https://github.com/Alcadas/alcadas-docs/pull/8) | 18/10 | Visão do produto, requisitos e histórias de usuário | Szervinsk | — |
| docs | [#9](https://github.com/Alcadas/alcadas-docs/pull/9) | 18/10 | Ajuste do guia de contribuição | Szervinsk | — |
| docs | [#10](https://github.com/Alcadas/alcadas-docs/pull/10) | 20/10 | Estratégia de testes | leticiadsp | Szervinsk |
| docs | [#11](https://github.com/Alcadas/alcadas-docs/pull/11) | 20/10 | ADRs, máquina de estados, tabela de transições, OpenAPI v0.1 e DoD | Szervinsk | — |
| docs | [#12](https://github.com/Alcadas/alcadas-docs/pull/12) | 22/10 | Personas e cronograma | Szervinsk | phenric26 |
| core | [#1](https://github.com/Alcadas/alcadas-core/pull/1) | 18/10 | Entregas do DevOps no CP1 | phenric26 | — |
| core | [#2](https://github.com/Alcadas/alcadas-core/pull/2) | 18/10 | Estrutura, governança, CI e guia de contribuição | Szervinsk | — |
| core | [#5](https://github.com/Alcadas/alcadas-core/pull/5) | 21/10 | Correção do Docker Compose | phenric26 | — |
| core | [#6](https://github.com/Alcadas/alcadas-core/pull/6) | 22/10 | Compose subindo os emuladores | phenric26 | — |
| core | [#7](https://github.com/Alcadas/alcadas-core/pull/7) | 22/10 | Entregas do DevOps no CP1 (complemento) | phenric26 | — |
| core | [#4](https://github.com/Alcadas/alcadas-core/pull/4) | 22/10 | Entregas do Backend no CP1 | LuizFelipe81 | phenric26 |
| core | [#3](https://github.com/Alcadas/alcadas-core/pull/3) | 22/10 | Teste de isolamento de tenant | leticiadsp | phenric26 |

---

## 5. Reuniões da Sprint

| Data | Reunião | Principais decisões | Ata |
| :-: | :--- | :--- | :--- |
| 05/10/2026 | Kickoff e planejamento inicial | _a preencher pela equipe_ | _a preencher pela equipe_ |
| 12/10/2026 | Sincronização S2 e preparação do gate | _a preencher pela equipe_ | _a preencher pela equipe_ |
| 18/10/2026 | Apresentação do Checkpoint 1 | _a preencher pela equipe_ | _a preencher pela equipe_ |

---

## 6. Controle de Custos (FinOps)

| Indicador | Valor |
| :--- | :-: |
| Meta do Caderno no CP1 | Alertas de orçamento em 50/80/100 % |
| Meta adotada | US$ 0,00 — Plano Custo Zero ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md), decidido no CP2) |
| Gasto na Sprint 1 | US$ 0,00 (desenvolvimento só em emuladores) |
| Evidência | ⏳ print do projeto Firebase no plano Spark, ver [FinOps](../finops/index.md) |

---

## 7. Pendências e Impedimentos

| Item | Responsável | Impacto | Ação / Prazo |
| :--- | :--- | :--- | :--- |
| 9 de 13 PRs mergeados sem revisão de outra pessoa | Tech Lead | Quebra o item 6 da DoD | Proteção de branch e CODEOWNERS revisados no CP2 ([Regras do repositório](../governanca/regras-repositorio.md)) |
| Entregas do CP1 mergeadas depois do gate (20 a 22/10) | Toda a equipe | O gate foi avaliado com parte do material fora da `main` | Abrir os PRs do gate até a quinta-feira anterior |
| Voucher do GCP não disponível | Tech Lead / DevOps | Sem conta de faturamento, os alertas de orçamento e o Cloud Run do Caderno ficam inviáveis | Resolvido no CP2 com a [ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md) |
| Registro das entrevistas e da apresentação do gate | PO | Sem evidência no repositório | _a preencher pela equipe_ |

---

## 8. Riscos Identificados

| Risco | Probabilidade | Impacto | Mitigação |
| :--- | :-: | :-: | :--- |
| Teste de isolamento de tenant ainda pendente | Média | Alto (`RNF07`) | Implementar no CP2 junto com a API real |
| Divergência entre rotas da rastreabilidade (inglês) e do OpenAPI (português) | Alta | Médio | Resolvida no CP2 pela [ADR-009](../arquitetura/adrs/ADR-009-contrato-api-v0-2.md) |

---

## 9. Feedback do Checkpoint 1

- **Tutor/Banca:** _a preencher pela equipe_
- **Cliente (Startop LTDA):** _a preencher pela equipe_

---

## 10. Retrospectiva

| O que funcionou bem | O que pode melhorar | Ações para a Sprint 2 |
| :--- | :--- | :--- |
| _a preencher pela equipe_ | _a preencher pela equipe_ | _a preencher pela equipe_ |

---

## 11. Próximos Passos — Sprint 2

Foco da [Sprint 2](../visao-produto/cronograma.md#sprint-2-nucleo-funcional-e-regras-de-seguranca-1910-a-01112026): núcleo funcional e regras de segurança (19/10 a 01/11/2026).

- [x] Jornadas 1 a 3 rodando localmente (centros de custo e tetos, submissão, decisão) — [Sprint 2](relatorio-sprint-2.md)
- [ ] Revisão obrigatória por outra pessoa em todo PR (na Sprint 2, 12 de 13 PRs tiveram aprovação)
- [x] Decisão sobre hospedagem sem o voucher do GCP ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md))
