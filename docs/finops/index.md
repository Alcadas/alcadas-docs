# FinOps — Custo Zero

O Alçadas segue o **Plano Custo Zero — sem o voucher do GCP** ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)): frontend no Firebase Hosting, login e banco no Firebase Auth e no Firestore (plano **Spark**) e API no **Render** (plano gratuito). **Nenhuma forma de pagamento é cadastrada em nenhum serviço**, então o pior que pode acontecer é um serviço parar — nunca uma cobrança.

**Meta: US$ 0,00 em todo o ciclo.**

| Checkpoint | Meta do Caderno | Versão custo zero | Situação |
| :--- | :--- | :--- | :--- |
| CP1 — Fundação (18/10) | Alertas de orçamento 50/80/100 % | Print do projeto Firebase no Spark, sem conta de faturamento | ⏳ |
| CP2 — Núcleo funcional (01/11) | Gasto ≤ US$ 5 | Gasto = US$ 0 + decisão registrada em ADR ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)) | ⏳ prints |
| CP3 — Nuvem e integração (15/11) | Gasto ≤ US$ 12 | Gasto = US$ 0 | — |
| CP4 — Estabilização (29/11) | Auditoria de custo e congelamento em US$ 40 | Auditoria das cotas usadas (Firestore, Hosting, Render, Actions) | — |

---

## 1. Evidências (substituem os alertas de orçamento)

Responsável: DevOps, com apoio do Tech Lead. Salvar os prints nesta pasta (`docs/finops/`) e enviar ao PO.

| # | Serviço | Onde tirar o print | Deve mostrar | Arquivo |
| :-: | :--- | :--- | :--- | :--- |
| 1 | Firebase | Console → Configurações do projeto → **Uso e faturamento** | Plano **Spark**, nenhuma conta de faturamento vinculada | `cp2-firebase-spark.png` |
| 2 | Firestore | Console → Firestore → **Uso** | Leituras, escritas e exclusões do período, longe dos limites diários | `cp2-firestore-uso.png` |
| 3 | Render | Dashboard → **Billing** do workspace | Plano gratuito, **nenhuma forma de pagamento**, horas de instância usadas | `cp2-render-billing.png` |
| 4 | GitHub | Organização `Alcadas` → Settings → **Billing and plans** | Plano gratuito, sem forma de pagamento | `cp2-github-billing.png` |

| Item | Valor | Data da leitura |
| :--- | :--- | :--- |
| Gasto total no ciclo | _preencher conforme os painéis_ | _dd/mm_ |

> Os valores vêm dos painéis, não de estimativa.

---

## 2. Cotas que podem parar o sistema

Limites conferidos pelo laboratório em 14/10/2026; o DevOps confere de novo antes do primeiro deploy.

| Serviço | Limite gratuito | Ao estourar |
| :--- | :--- | :--- |
| Firestore | 1 GiB armazenado; 50 mil leituras, 20 mil escritas e 20 mil exclusões por dia | Para de aceitar leitura ou escrita até o dia seguinte |
| Firebase Auth | 50 mil usuários ativos por mês | Novos logins recusados |
| Firebase Hosting | 10 GB armazenados; 360 MB transferidos por dia | Site fora do ar até o dia seguinte |
| Render (web service) | 750 horas por mês por workspace; dorme após 15 min sem uso | API suspensa até o mês seguinte |
| UptimeRobot | 50 monitores, checagem a cada 5 min | — |

---

## 3. Regras de ouro

1. **Nunca cadastrar cartão** em nenhum serviço: Google Cloud, Firebase, Render, GitHub, UptimeRobot. Se algum pedir cartão "só para verificar", não cadastre e avise o tutor.
2. **Nunca clicar em "Upgrade" ou "Blaze"** no console do Firebase, nem vincular conta de faturamento ao projeto.
3. **Testes pesados só no emulador:** carga (k6), testes automatizados, testes de concorrência e seed grande rodam no `docker compose`, nunca contra o Firestore real.
4. Um projeto Firebase da equipe, criado por um integrante e compartilhado com os demais como membros.
5. Conta de serviço com papel mínimo; chave só nos secrets do GitHub e nas variáveis do Render; `gitleaks` no CI.
6. Toda listagem paginada (`limit` ≤ 50) e toda consulta filtrada por `tenantId` ([OpenAPI v0.2](../api/index.md)); nada de *listeners* do Firestore no navegador ([ADR-008](../arquitetura/adrs/ADR-008-firestore-deny-all.md)).
7. Em caso de dúvida, não ative: serviço novo só com ADR e aprovação do Tech Lead ([DoD](../governanca/dod.md)).

---

## 4. Se uma cota estourar

Registrar em `docs/finops/incidente-<data>.md`: qual cota, quando, impacto e o que foi feito. Exemplo de formato: *"Cota diária de escritas do Firestore esgotada em 27/11 por um seed rodado contra produção; seed passou a rodar só no emulador; banco voltou no dia seguinte."*
