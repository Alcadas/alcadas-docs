# Relatório da Sprint 3 — Nuvem, Integração Contínua e Observabilidade

| Atributo | Descrição |
| :--- | :--- |
| **Sprint** | Sprint 3 (Semanas S5 e S6) |
| **Período** | 02/11/2026 a 15/11/2026 (PRs mergeados de 03/11 a 15/11) |
| **Checkpoint** | Checkpoint 3 — Nuvem e Integração (gate em 15/11/2026) |
| **Cliente/Parceiro** | Startop LTDA — Julia Campos Santana |
| **Responsável pelo relatório** | Henrique Schneider (Tech Lead) |
| **Data de emissão** | 22/11/2026 |

---

## 1. Objetivo da Sprint

Publicar o Alçadas em nuvem no Plano Custo Zero (frontend no Firebase Hosting, API no Render, Auth e Firestore no Spark), com observabilidade e infraestrutura segura, conforme o [Cronograma](../visao-produto/cronograma.md#sprint-3-nuvem-integracao-continua-e-observabilidade-0211-a-15112026).

---

## 2. Resumo Executivo

Foi a sprint mais intensa: 20 PRs mergeados. O épico de segurança (`alcadas-core#26`) teve todas as filhas entregues:

- validação da assinatura do JWT (`#27`);
- RBAC em `saas/tenants` (`#28`);
- Firestore real via Admin SDK com `/readyz` (`#29`);
- provisionamento dos colaboradores no Firebase Auth e seed de produção (`#32`);
- CORS restrito (`#34`);
- logs JSON com `requestId` (`#35`).

A infraestrutura ganhou o Dockerfile de produção e o blueprint do Render (`#46`) e o Firebase Hosting com Security Rules *deny-all* (`#43`). Das dívidas do CP2, quatro de cinco foram fechadas: integração ponta a ponta, recuperação de senha, construtor de regras e tutoriais.

Ficaram para depois do gate:

- pipeline de CI/CD com preview e deploy automático (`#48`, não iniciado);
- monitoramento com UptimeRobot e guia de rollback (`#53`, não iniciado);
- smoke tests e ensaio com usuário externo (`#57`);
- aval do professor para as exceções da ADR-011 (`alcadas-docs#19`).

Das 25 issues do CP3 nos dois repositórios, **7 estavam fechadas no dia do gate**.

| Indicador | Valor |
| :--- | :-: |
| Issues do Checkpoint 3 | 25 planejadas · 7 concluídas até 15/11 |
| Dívidas do CP2 concluídas na sprint | 4 de 5 (`#65`, `#66`, `#68`, `#69`; `#67` em 19/11) |
| Pull Requests mergeados | 20 (19 no `alcadas-core`, 1 no `alcadas-docs`) |
| PRs com aprovação de outra pessoa | 19 de 20 |
| Gasto em nuvem | US$ 0,00 (meta do Plano Custo Zero) — prints pendentes (`alcadas-docs#18`) |
| Resultado do Checkpoint 3 | _a preencher pela equipe_ |

---

## 3. Entregas do Checkpoint 3 por Papel

Legenda de status: ✅ Concluído · 🟡 Parcial · ⏳ Pendente · ➡️ Entregue em sprint posterior

### 3.1. PO — Matheus Ribeiro Szervinsk

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Plataforma SaaS multi-tenant e esteira de aprovação no frontend | ✅ | `alcadas-core#25` | |
| Ajustes de interface e UX | ✅ | `alcadas-core#77` | |
| Onboarding interativo e central de tutoriais | ✅ | `alcadas-core#89` | Issue `#67` fechada em 19/11 |
| Homologação do gate e dossiê de evidências | ⏳ | `alcadas-core#60` | Épico aberto |

### 3.2. Tech Lead — Henrique Schneider

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| CORS só para os domínios da equipe e canais de preview | ✅ | `alcadas-core#82` (`#34`) | |
| Logs JSON com `requestId`, sem dados pessoais | ✅ | `alcadas-core#83` (`#35`) | `RNF09` |
| Firestore real via Admin SDK, sem fallback em memória em produção, `/readyz` | ✅ | `alcadas-core#84` (`#29`) | Não testado no Render até o gate |
| Provisionamento no Firebase Auth e seed idempotente de produção | ✅ | `alcadas-core#85` (`#32`) | |
| RBAC em `saas/tenants` com testes de autorização | ✅ | `alcadas-core#87` (`#28`) | `PUT` também aceita o `ADMIN` da própria empresa |
| OpenAPI v0.2, ADR-008 a ADR-011 e DoD na `main` | ✅ | `alcadas-docs#17` | Entrega do CP2 mergeada em 14/11 |
| Aval do professor para as exceções | ⏳ | `alcadas-docs#19` | Sem resposta registrada |
| Cronograma/DoD, OpenAPI v0.3 e cotas do Firestore | ➡️ | `alcadas-docs#21`, `#22`, `#23` | Preparados em 22/11 |

### 3.3. DevOps — Pedro Henrique Gomes

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Firebase Hosting com rewrites de SPA e Security Rules *deny-all* | ✅ | `alcadas-core#73` (`#43`) | |
| Dockerfile de produção (usuário não-root) e blueprint do Render | ✅ | `alcadas-core#71` (`#46`) | Issue `#46` segue aberta |
| Otimização dos containers | ✅ | `alcadas-core#63` | Mergeado sem aprovação |
| Integração ponta a ponta de fluxos, solicitações e aprovações | ✅ | `alcadas-core#74` (`#66`) | |
| Validação da assinatura do JWT | ✅ | `alcadas-core#75` (`#27`) | |
| Isolamento departamental e atualização automática da lista | ✅ | `alcadas-core#76` | Ver risco de cota abaixo |
| Ambiente de produção no frontend | ➡️ | `alcadas-core#101` (`#38`) | Mergeado em 22/11 |
| Credencial com papel mínimo e Gitleaks no CI | ➡️ | `alcadas-core#102` (`#51`) | Mergeado em 22/11 |
| CI/CD com preview em PR e deploy automático | ⏳ | `alcadas-core#48` | Não iniciado |
| UptimeRobot e documentação de deploy/rollback | ⏳ | `alcadas-core#53` | Não iniciado |
| Evidências de custo | ⏳ | `alcadas-docs#18` | |

### 3.4. QA — Leticia

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Construtor de regras com modelos pré-definidos | ✅ | `alcadas-core#80` (`#69`) | |
| Smoke tests de produção e ensaio com usuário externo | ⏳ | `alcadas-core#57` | Critério do gate do CP3 |
| Observabilidade e UX de cold start (épico) | ⏳ | `alcadas-core#54` | |

### 3.5. Front/Back — Ana Clara e Luis

| Item | Status | Evidência | Observações |
| :--- | :-: | :--- | :--- |
| Fluxo de recuperação e redefinição de senha | ✅ | `alcadas-core#78` (`#68`) | |
| Lógica de fluxos integrada ao construtor | ✅ | `alcadas-core#88` (`#69`) | |
| Tratar a espera enquanto a API acorda | ⏳ | `alcadas-core#40` | Cold start de ~1 min no Render |

---

## 4. Histórico de Pull Requests

| Repositório | PR | Merge | Descrição | Autor | Revisor |
| :--- | :-: | :-: | :--- | :--- | :--- |
| core | [#25](https://github.com/Alcadas/alcadas-core/pull/25) | 03/11 | Plataforma SaaS multi-tenant e esteira de aprovação | Szervinsk | phenric26 |
| core | [#63](https://github.com/Alcadas/alcadas-core/pull/63) | 04/11 | Otimização dos containers | phenric26 | — |
| core | [#64](https://github.com/Alcadas/alcadas-core/pull/64) | 05/11 | Entregas de DevOps do CP2 (para a `main`) | phenric26 | Szervinsk |
| core | [#71](https://github.com/Alcadas/alcadas-core/pull/71) | 05/11 | Dockerfile de produção e blueprint do Render (`#46`) | phenric26 | SchneiderCode1 |
| core | [#73](https://github.com/Alcadas/alcadas-core/pull/73) | 06/11 | Firebase Hosting e Security Rules (`#43`) | phenric26 | Szervinsk |
| core | [#74](https://github.com/Alcadas/alcadas-core/pull/74) | 08/11 | Integração ponta a ponta (`#66`) | phenric26 | leticiadsp |
| core | [#75](https://github.com/Alcadas/alcadas-core/pull/75) | 09/11 | Assinatura do JWT em produção (`#27`) | phenric26 | Szervinsk |
| core | [#76](https://github.com/Alcadas/alcadas-core/pull/76) | 09/11 | Isolamento departamental e atualização automática | phenric26 | Szervinsk |
| core | [#77](https://github.com/Alcadas/alcadas-core/pull/77) | 10/11 | Ajustes de interface e UX | Szervinsk | phenric26 |
| core | [#78](https://github.com/Alcadas/alcadas-core/pull/78) | 10/11 | Recuperação de senha (`#68`) | anacllaraxy | Szervinsk |
| core | [#79](https://github.com/Alcadas/alcadas-core/pull/79) | 10/11 | Release da `develop` para a `main` | phenric26 | SchneiderCode1, Szervinsk, leticiadsp |
| core | [#80](https://github.com/Alcadas/alcadas-core/pull/80) | 13/11 | Construtor de regras com modelos (`#69`) | leticiadsp | Szervinsk |
| core | [#82](https://github.com/Alcadas/alcadas-core/pull/82) | 14/11 | CORS restrito (`#34`) | SchneiderCode1 | Szervinsk, phenric26 |
| core | [#83](https://github.com/Alcadas/alcadas-core/pull/83) | 14/11 | Logs JSON com `requestId` (`#35`) | SchneiderCode1 | Szervinsk |
| core | [#84](https://github.com/Alcadas/alcadas-core/pull/84) | 14/11 | Firestore real e `/readyz` (`#29`) | SchneiderCode1 | phenric26 |
| core | [#85](https://github.com/Alcadas/alcadas-core/pull/85) | 14/11 | Provisionamento no Auth e seed de produção (`#32`) | SchneiderCode1 | phenric26 |
| core | [#87](https://github.com/Alcadas/alcadas-core/pull/87) | 14/11 | RBAC em `saas/tenants` (`#28`) | SchneiderCode1 | phenric26 |
| core | [#88](https://github.com/Alcadas/alcadas-core/pull/88) | 14/11 | Lógica de fluxos integrada (`#69`) | LuizFelipe81 | Szervinsk |
| core | [#89](https://github.com/Alcadas/alcadas-core/pull/89) | 15/11 | Onboarding e central de tutoriais (`#67`) | Szervinsk | phenric26 |
| docs | [#17](https://github.com/Alcadas/alcadas-docs/pull/17) | 14/11 | OpenAPI v0.2, ADR-008 a ADR-011, DoD e FinOps | SchneiderCode1 | phenric26 |

---

## 5. Reuniões da Sprint

| Data | Reunião | Principais decisões | Ata |
| :-: | :--- | :--- | :--- |
| 02/11/2026 | Planejamento da Sprint 3 e review do Gate 2 | _a preencher pela equipe_ | _a preencher pela equipe_ |
| 09/11/2026 | Acompanhamento S6 e preparação do Gate 3 | _a preencher pela equipe_ | _a preencher pela equipe_ |
| 15/11/2026 | Apresentação do Checkpoint 3 | _a preencher pela equipe_ | _a preencher pela equipe_ |

---

## 6. Controle de Custos (FinOps)

| Indicador | Valor |
| :--- | :-: |
| Meta do Caderno no CP3 | Gasto ≤ US$ 12,00 |
| Meta adotada | US$ 0,00 + uso das cotas do Firestore |
| Gasto na Sprint 3 | US$ 0,00 esperado (nenhum serviço com forma de pagamento) — **a confirmar com os prints** (`alcadas-docs#18`) |
| Cotas do Firestore | Estimativa por jornada na página `finops/cotas-firestore.md`, proposta em `alcadas-docs#23` |

---

## 7. Pendências e Impedimentos

| Item | Responsável | Impacto | Ação / Prazo |
| :--- | :--- | :--- | :--- |
| Aval do professor para as 4 exceções e para os alertas de 5xx/p95 | Tech Lead | Sem aval, o CP3 pode ser reprovado | Registrar na ADR-011 (`alcadas-docs#19`) |
| CI/CD com preview e deploy automático não iniciado | DevOps | Deploy manual; sem link de preview nos PRs | `alcadas-core#48` |
| Monitoramento e guia de rollback não iniciados | DevOps | Queda da API sem alerta; rollback sem roteiro | `alcadas-core#53` |
| Smoke tests e ensaio com usuário externo | QA | Evidência principal do gate do CP3 | `alcadas-core#57` |
| Issue `#46` aberta com a entrega já mergeada | DevOps | Painel do milestone mostra trabalho a mais | Fechar a issue |
| Prints de custo dos serviços | DevOps | FinOps sem evidência | `alcadas-docs#18` |

---

## 8. Riscos Identificados

| Risco | Probabilidade | Impacto | Mitigação |
| :--- | :-: | :-: | :--- |
| **Cota diária de leituras do Firestore estourar no Demo Day** por causa da atualização automática a cada 8 s (~24 mil leituras por hora por aba) | Alta | Alto | Atualização automática mais barata e paginação por cursor, propostas em `alcadas-docs#23` |
| Cold start de ~1 min no Render | Certa | Médio | Acordar a API antes da demo; tela de espera (`alcadas-core#40`) |
| Chave da conta de serviço vazar | Baixa | Alto | Papel mínimo e Gitleaks no CI (`alcadas-core#102`) |
| Exceções da ADR-011 não aceitas | Média | Alto | `alcadas-docs#19` |

---

## 9. Feedback do Checkpoint 3

- **Tutor/Banca:** _a preencher pela equipe_
- **Cliente (Startop LTDA):** _a preencher pela equipe_

---

## 10. Retrospectiva

| O que funcionou bem | O que pode melhorar | Ações para a Sprint 4 |
| :--- | :--- | :--- |
| _a preencher pela equipe_ | _a preencher pela equipe_ | _a preencher pela equipe_ |

---

## 11. Próximos Passos — Sprint 4

Foco da [Sprint 4](../visao-produto/cronograma.md#sprint-4-qualidade-estabilizacao-e-auditoria-1611-a-29112026): qualidade, estabilização e auditoria (16/11 a 29/11/2026), com *feature freeze*.

- [ ] Fechar as pendências do CP3: `#48`, `#53`, `#57` e o aval do professor
- [ ] Reduzir o consumo de leituras do Firestore antes do Demo Day
- [ ] Ensaio de rollback no Render e no Firebase Hosting
- [ ] Cobertura ≥ 80 % no domínio e ≥ 60 % global
