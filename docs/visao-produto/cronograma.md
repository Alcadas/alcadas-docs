# Cronograma do Projeto e Plano de Execução

Este documento estabelece o **planejamento temporal, a cadência ágil e o cronograma de marcos (gates)** do projeto **Alçadas**, estruturado estritamente em conformidade com o **Caderno de Projetos B2B v2 (AI LAB / MAKERS)** e o documento oficial de [Checkpoints e Pontos de Controle B2B](../assets/Checkpoints_e_Pontos_de_Controle_B2B.pdf).

O ciclo total compreende **9 semanas de execução (04/10/2026 a 05/12/2026)**, organizado em **Sprints de 2 (duas) semanas**, com **reuniões semanais de alinhamento e controle sempre às segundas-feiras**.

---

## 1. Estrutura do Ciclo e Modelo de Governança

```mermaid
flowchart LR
    subgraph Sprint1["Sprint 1 (S1-S2)"]
        direction TB
        M1["05/10: Kickoff & Planejamento"] --> M2["12/10: Acompanhamento S2"]
        M2 --> CP1["18/10 · Gate 1: Fundação"]
    end

    subgraph Sprint2["Sprint 2 (S3-S4)"]
        direction TB
        M3["19/10: Planejamento Sprint 2"] --> M4["26/10: Acompanhamento S4"]
        M4 --> CP2["01/11 · Gate 2: Núcleo Funcional"]
    end

    subgraph Sprint3["Sprint 3 (S5-S6)"]
        direction TB
        M5["02/11: Planejamento Sprint 3"] --> M6["09/11: Acompanhamento S6"]
        M6 --> CP3["15/11 · Gate 3: Nuvem & Integração"]
    end

    subgraph Sprint4["Sprint 4 (S7-S8)"]
        direction TB
        M7["16/11: Planejamento Sprint 4"] --> M8["23/11: Acompanhamento S8"]
        M8 --> CP4["29/11 · Gate 4: Estabilização"]
    end

    subgraph Sprint5["Sprint 5 (S9)"]
        direction TB
        M9["30/11: Ensaio Geral"] --> CP5["05/12 · Gate 5: Demo Day"]
    end

    Sprint1 --> Sprint2 --> Sprint3 --> Sprint4 --> Sprint5
```

### 1.1. Regras Operacionais da Equipe
- **Duração das Sprints:** 2 semanas para desenvolvimento incremental (Sprints 1 a 4) + 1 semana dedicada para homologação final e ensaios (Sprint 5);
- **Reuniões de Sincronização:** Todas as **segundas-feiras (20 a 30 minutos)**, estruturadas sob o roteiro padrão de 7 perguntas do Guia B2B;
- **Critério de Passagem (Regra de Gate):** O avanço para a sprint subsequente exige que as evidências estejam registradas no repositório, com testes passando, CI verde e validação reproduzível por outro membro da equipe (*Definition of Done*). Não basta "funcionar na máquina do desenvolvedor".

---

## 2. Visão Geral das Sprints e Checkpoints Oficiais

> [!NOTE] Plano Custo Zero
> Desde o Checkpoint 2 o projeto segue o **Plano Custo Zero — sem o voucher do GCP** ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)): frontend no **Firebase Hosting**, login e banco no **Firebase Auth + Firestore (plano Spark)** e API no **Render** (web service gratuito). Não há conta de faturamento, então a meta é **US$ 0,00 em todos os checkpoints**; os tetos do Caderno (US$ 5, 12, 40 e 50) ficam só como referência. As exceções ao Caderno dependem do aval do professor, registrado na própria ADR-011.

| Sprint / Período | Semanas | Gate Oficial B2B | Data Limite | Objetivo Principal | Meta FinOps (Custo Zero) | Entregáveis Críticos |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Sprint 1**<br>05/10 a 18/10/2026 | S1 e S2 | **Checkpoint 1 — Fundação** | **18/10/2026** | Validar problema, escopo, arquitetura e ambiente local. | US$ 0,00<br>*(100% emuladores)* | Visão do Produto, Ishikawa, Backlog MoSCoW, 7 ADRs, OpenAPI v0.1, DoD, Estratégia de Testes, Monorepo Nx, Docker Compose com emuladores. |
| **Sprint 2**<br>19/10 a 01/11/2026 | S3 e S4 | **Checkpoint 2 — Núcleo Funcional** | **01/11/2026** | Validar o fluxo ponta a ponta das jornadas do MVP rodando localmente. | US$ 0,00 + decisão em ADR<br>*(Caderno: ≤ US$ 5)* | 3 a 5 jornadas funcionais locais, formulários reativos, máquina de estados atômica, anti-fracionamento, Security Rules *deny-all*, testes EP/BVA v1, OpenAPI v0.2, ADR-011. |
| **Sprint 3**<br>02/11 a 15/11/2026 | S5 e S6 | **Checkpoint 3 — Nuvem e Integração** | **15/11/2026** | Validar publicação em nuvem, observabilidade e infraestrutura segura. | US$ 0,00 + uso das cotas do Firestore<br>*(Caderno: ≤ US$ 12)* | Front no Firebase Hosting com preview no PR, API no Render com CORS restrito ao domínio da equipe, `/healthz` e `/readyz`, logs JSON com `requestId`, chave de serviço com papel mínimo + `gitleaks` no CI, UptimeRobot em `/api/readyz`. |
| **Sprint 4**<br>16/11 a 29/11/2026 | S7 e S8 | **Checkpoint 4 — Qualidade e Estabilização** | **29/11/2026** | Congelamento de escopo (*feature freeze*), confiabilidade e ensaio de contingência. | US$ 0,00 + auditoria das cotas<br>*(Caderno: alerta em US$ 40)* | Cobertura ≥80% domínio / ≥60% global, zero bugs críticos, zero vulnerabilidade alta/crítica, Lighthouse ≥90, rollback ensaiado no Render e no Hosting, exportação Firestore. |
| **Sprint 5**<br>30/11 a 05/12/2026 | S9 | **Checkpoint Final — Demo Day** | **05/12/2026** | Homologação final, validação da defensibilidade e apresentação do produto. | US$ 0,00 no ciclo inteiro<br>*(Caderno: ≤ US$ 50)* | Produção estável, API acordada antes da demo (cold start do Render), smoke test pós-deploy verde, demonstração ao vivo da defensibilidade (< 1 s em anti-fracionamento e quebra de hash SHA-256). |

---

## 3. Detalhamento das Sprints e Agenda de Reuniões de Segunda-Feira

### Sprint 1: Fundação e Arquitetura Base (05/10 a 18/10/2026)
*Foco: Problema, escopo, arquitetura, ambiente emulado e governança.*

- **Reunião 1 (Segunda-feira, 05/10/2026) — Kickoff & Planejamento Inicial:**
  - Alinhamento da parceria com **Julia Campos Santana** ([Startop LTDA](visao-produto.md#11-identificacao-do-clienteparceiro));
  - Elaboração da [Visão de Produto](visao-produto.md) e do Diagrama de Ishikawa (6M's);
  - Definição da stack tecnológica (Angular, NestJS, Firestore, Firebase Auth, Docker);
  - Distribuição dos papéis da equipe (PO, Tech Lead, DevOps, Backend, Frontend, QA).
- **Reunião 2 (Segunda-feira, 12/10/2026) — Sincronização S2 & Preparação do Gate 1:**
  - Avaliação do monorepo Nx e orquestração do `docker compose up` com emuladores;
  - Revisão da [Estratégia de Testes](../testes/estrategia_de_testes.md) e do [Definition of Done](../governanca/dod.md);
  - Redação das ADRs fundamentais ([ADR-001](../arquitetura/adrs/ADR-001-monorepo-nx.md) a [ADR-007](../arquitetura/adrs/ADR-007-rbac-e-sod.md)) e especificação [OpenAPI v0.1](../api/index.md).
- **Entrega Oficial — Checkpoint 1 (Domingo, 18/10/2026):**
  - **Evidências auditadas:** Repositório configurado; CI com lint e build verdes; seed de dados reproduzível; 0 segredos expostos (`.env.example` sanitizado). Os alertas de orçamento do GCP em 50%, 80% e 100% previstos no Caderno foram substituídos, a partir do CP2, pelo print de cada serviço no plano gratuito, sem forma de pagamento ([FinOps](../finops/index.md)).

---

### Sprint 2: Núcleo Funcional e Regras de Segurança (19/10 a 01/11/2026)
*Foco: Jornadas principais do MVP rodando localmente, máquina de estados e validações de risco.*

- **Reunião 3 (Segunda-feira, 19/10/2026) — Planejamento da Sprint 2 & Review do Gate 1:**
  - Apresentação e homologação das [Personas do Usuário](personas.md) (Carlos, Mariana e Patrícia);
  - Decomposição das histórias do grupo **Must** do MoSCoW:
    - [`US-01`](../historias-usuario.md#41-must-essencial-para-o-mvp-e-o-demo-day): Parametrização de centros de custo e faixas de alçada (`RF01`);
    - [`US-02`](../historias-usuario.md#41-must-essencial-para-o-mvp-e-o-demo-day): Submissão de compras com formulários reativos e justificativa (`RF02`);
    - [`US-03`](../historias-usuario.md#41-must-essencial-para-o-mvp-e-o-demo-day): Painel de decisão do aprovador (`RF03`);
    - [`US-05`](../historias-usuario.md#41-must-essencial-para-o-mvp-e-o-demo-day): Tratamento de concorrência com transações atômicas (`RF11`);
    - [`US-06`](../historias-usuario.md#41-must-essencial-para-o-mvp-e-o-demo-day): Motor anti-fracionamento em janela de 30 dias (`RF05`);
    - [`US-07`](../historias-usuario.md#41-must-essencial-para-o-mvp-e-o-demo-day): Cadeia de hashes SHA-256 encadeada (`RF07`).
- **Reunião 4 (Segunda-feira, 26/10/2026) — Acompanhamento S4 & Preparação do Gate 2:**
  - Checagem dos testes de autorização e Security Rules *deny-all* no Firestore;
  - Validação dos estados visuais de tela (*loading*, *empty*, *error* conforme `RNF08`);
  - Verificação de FinOps: gasto US$ 0,00 e registro do Plano Custo Zero na [ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md) (o Caderno permitiria até US$ 5,00).
- **Entrega Oficial — Checkpoint 2 (Domingo, 01/11/2026):**
  - **Evidências auditadas:** Demonstração da jornada completa localmente sem intervenção manual; suíte EP/BVA v1 executando; matriz de rastreabilidade Requisito $\to$ Aceite $\to$ Teste atualizada; bloqueio efetivo de tentativas de autoaprovação (*SoD* - `RF12`).

---

### Sprint 3: Nuvem, Integração Contínua e Observabilidade (02/11 a 15/11/2026)
*Foco: Deploy contínuo em ambiente de homologação/produção, métricas operacionais e segurança de infraestrutura.*

- **Reunião 5 (Segunda-feira, 02/11/2026) — Planejamento da Sprint 3 & Review do Gate 2:**
  - Pedido de aval do professor para as exceções da [ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md) — **primeira atividade do checkpoint**;
  - Planejamento do pipeline de entrega contínua: **Firebase Hosting** (Frontend Angular) com canal de preview em cada PR e **Render** (Backend NestJS) com deploy automático no merge da `main` após CI verde;
  - Publicação da API a partir do Dockerfile de produção (usuário não-root) no web service gratuito do Render;
  - Chamada direta do front para a URL do Render, com **CORS liberado só para o domínio `.web.app`/`.firebaseapp.com` da equipe** e seus canais de preview (substitui o rewrite `/api` de mesma origem);
  - Credencial da API: **chave da conta de serviço do Firebase Admin com papel mínimo**, guardada só nos secrets do GitHub e nas variáveis do Render, com `gitleaks` no CI (substitui o Workload Identity Federation).
- **Reunião 6 (Segunda-feira, 09/11/2026) — Acompanhamento S6 & Preparação do Gate 3:**
  - Implementação dos endpoints de sondagem operacional `/healthz` (processo vivo, usado pelo health check do Render) e `/readyz` (Firestore acessível);
  - Padronização de logs estruturados em formato JSON com `requestId`, `rota`, `status`, `latência` e ausência de dados PII (`RNF09`), lidos no painel de logs do Render;
  - Monitoramento externo com **UptimeRobot em `/api/readyz`** e alerta por e-mail (substitui o Cloud Monitoring). Os alertas de erros `5xx` e de latência ($p95 > 1{,}5\text{ s}$) do Caderno não têm equivalente gratuito e seguem a decisão registrada na ADR-011;
  - Auditoria de FinOps: [leituras e escritas do Firestore por jornada](../finops/index.md) frente às cotas do Spark (50 mil leituras e 20 mil escritas por dia) e conferência do gasto (US$ 0,00).
- **Entrega Oficial — Checkpoint 3 (Domingo, 15/11/2026):**
  - **Evidências auditadas:** Usuário externo acessa a URL de homologação, efetua login via Firebase Auth, cria uma solicitação e a decisão é refletida em tempo real sem erros de CORS ou dependência de ambiente local.

---

### Sprint 4: Qualidade, Estabilização e Auditoria (16/11 a 29/11/2026)
*Foco: Congelamento de escopo (Feature Freeze), cobertura rigorosa, testes de carga, ensaio de rollback e blindagem de segurança.*

- **Reunião 7 (Segunda-feira, 16/11/2026) — Planejamento da Sprint 4 & Feature Freeze:**
  - Decretado o **Congelamento de Escopo (*Feature Freeze*)** — proibida a inclusão de novas features; foco total em confiabilidade;
  - Reforço da suíte de testes de integração e ponta a ponta (`US-08` — detecção de registros adulterados em menos de 1 s);
  - Testes das histórias do grupo **Should**: delegação temporária (`US-10`), resolução de ajustes (`US-11`), duplicidade (`US-12`) e exportação CSV/JSON (`US-13`).
- **Reunião 8 (Segunda-feira, 23/11/2026) — Acompanhamento S8 & Ensaio de Contingência:**
  - Execução e registro do **ensaio de rollback** em produção: deploy anterior no painel do Render e versão anterior no histórico do Firebase Hosting;
  - Teste de exportação manual e restauração da base Firestore;
  - Varredura de segurança contra vulnerabilidades (Dependabot, SAST, auditoria de imagens Docker com digest SHA);
  - Auditoria de performance Lighthouse ($> 90$ em Desktop/Mobile) e auditoria das cotas gratuitas usadas (Firestore, Hosting, Render e GitHub Actions), no lugar do limite preventivo de US$ 40,00 do Caderno.
- **Entrega Oficial — Checkpoint 4 (Domingo, 29/11/2026):**
  - **Evidências auditadas:** Cobertura de testes $\ge 80\%$ no domínio e $\ge 60\%$ global; zero vulnerabilidades críticas/altas; pipeline bloqueante em caso de falha; relatório de qualidade publicado em `/docs/testes`.

---

### Sprint 5: Reta Final, Homologação e Demo Day (30/11 a 05/12/2026)
*Foco: Homologação em ambiente de produção, ensaios da apresentação e defesa técnica perante a banca.*

- **Reunião 9 (Segunda-feira, 30/11/2026) — Ensaio Geral & Roteiro da Demonstração:**
  - Alinhamento do roteiro da apresentação executiva com foco em:
    1. **Problema e Contexto:** A dor da Startop LTDA (Ishikawa e perdas financeiras por aprovações informais);
    2. **Solução e Arquitetura:** Máquina de estados atômica e segregação estrita de funções (*SoD*);
    3. **Apresentação da Jornada Principal:** Carlos solicitando, Mariana aprovando e Patrícia auditando;
    4. **Comprovação da Defensibilidade:** Demonstração ao vivo do bloqueio de compras fracionadas em < 1 s e quebra detectada na cadeia SHA-256 após edição direta no banco de dados;
    5. **Evidências de Engenharia:** Métricas de cobertura, observabilidade, conformidade com a LGPD e relatório final de custos FinOps.
- **Quarta a Sexta-feira (02 a 04/12/2026):**
  - Rodadas de *dry run* (testes cegos de apresentação) com tempo cronometrado;
  - Verificação de estabilidade dos endpoints `/healthz` e `/readyz` em produção, acordando a API no Render 5 minutos antes de cada ensaio (cold start de ~1 minuto);
  - Preparação da massa de dados limpa e controlada (*seed* oficial da demo).
- **Checkpoint Final — Demo Day (Sábado, 05/12/2026):**
  - **Apresentação oficial e homologação do MVP Alçadas perante a banca examinadora e stakeholders.**

---

## 4. Rito da Reunião Semanal de Segunda-feira (As 7 Perguntas)

Todas as reuniões de segunda-feira seguem estritamente a pauta de 20 a 30 minutos estabelecida no Caderno B2B:

| # | Pergunta Guia | Evidência Exigida | Responsável Primário |
| :-: | :--- | :--- | :--- |
| **1** | **O que foi concluído?** | *Pull Request* aprovado, *issue* fechada no GitHub Projects ou feature demonstrada funcionando. | Tech Lead / Desenvolvedores |
| **2** | **O que está bloqueado?** | Impedimento registrado formalmente, com impacto nas histórias e indicação de responsável pela tratativa. | Toda a equipe / PO |
| **3** | **Qual critério foi coberto?** | Caso de teste automatizado implementado vinculado aos critérios de aceite da User Story. | QA Lead (Leticia) |
| **4** | **Qual risco aumentou?** | Registro atualizado na matriz de riscos, novo ADR elaborado ou plano de contingência acionado. | Tech Lead (Henrique) / PO (Matheus) |
| **5** | **Quanto já gastamos?** | Confirmação de que nenhum serviço tem forma de pagamento (gasto US$ 0,00) e leitura do uso das cotas gratuitas: Firestore, Hosting, horas do Render e minutos do GitHub Actions ([FinOps](../finops/index.md)). | DevOps (Pedro Henrique) |
| **6** | **O que será feito na próxima semana?** | Histórias priorizadas da sprint com responsáveis explícitos e critérios de pronto (*DoD*) revisados. | PO (Matheus) / Time de Desenvolvimento |
| **7** | **O que precisa de tutor/cliente?** | Dúvida técnica ou de negócio sintetizada de forma objetiva, com contexto e prazo limite para decisão. | PO em alinhamento com Julia Campos Santana |

---

## 5. Curva de Gastos FinOps (Plano Custo Zero)

No plano Spark e no web service gratuito do Render **não existe cobrança possível**: quando um limite gratuito estoura, o serviço para até o dia (ou o mês) seguinte. Por isso o controle deixa de ser uma curva de gasto e passa a ser o **acompanhamento das cotas**, com a meta de US$ 0,00 em todas as etapas ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)):

```mermaid
flowchart TD
    G1["S1-S2: Emuladores Locais\nGasto: US$ 0,00"] --> G2["S3-S4: Testes Locais e Seeds\nGasto: US$ 0,00 · ADR-011"]
    G2 --> G3["S5-S6: Hosting + Render + Firestore Spark\nGasto: US$ 0,00 · cotas do Firestore"]
    G3 --> G4["S7-S8: Carga só no emulador\nGasto: US$ 0,00 · auditoria das cotas"]
    G4 --> G5["S9: Demo Day em Produção\nGasto: US$ 0,00 · API acordada antes"]

    style G1 fill:#e8f5e9,stroke:#2e7d32
    style G2 fill:#e8f5e9,stroke:#2e7d32
    style G3 fill:#e3f2fd,stroke:#1565c0
    style G4 fill:#fff3e0,stroke:#e65100
    style G5 fill:#fbe9e7,stroke:#c62828
```

Os alertas de orçamento em 50%, 80% e 100% do Caderno dependem de conta de faturamento e não existem no Spark. No lugar deles:

- **Prova de custo zero:** print de cada serviço no plano gratuito, sem forma de pagamento, salvo em [`/docs/finops`](../finops/index.md) a cada checkpoint;
- **Cotas que podem parar o sistema:** Firestore (50 mil leituras e 20 mil escritas por dia), Render (750 horas por mês) e Hosting (360 MB transferidos por dia), acompanhadas na reunião de segunda-feira;
- **Contenção:** testes de carga, testes automatizados e seed grande rodam só no emulador; se uma cota estourar, o incidente é registrado em `docs/finops/incidente-<data>.md`.

---

> [!IMPORTANT] Rastreabilidade com a Definition of Done
> Cada marco estabelecido neste cronograma só é considerado cumprido quando os artefatos de código, testes, infraestrutura e documentação satisfizerem integralmente as regras da [Definition of Done (DoD)](../governanca/dod.md) do projeto.
