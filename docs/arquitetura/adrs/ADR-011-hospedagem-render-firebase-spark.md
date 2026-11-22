# ADR-011: Plano Custo Zero — API no Render e Firebase no Plano Spark (sem voucher do GCP)

## Status
**Aceito pela equipe** (Checkpoint 2) — **aguardando o aval do professor**, porque troca serviços que o Caderno B2B v2 define como obrigatórios. O pedido e a resposta ficam registrados na seção [Aval do professor](#aval-do-professor).
Substitui as partes de deploy, operação e orçamento que dependiam do GCP faturado. Mantém o [ADR-004](ADR-004-firestore-e-firebase-auth.md) (Firestore + Firebase Auth) e o [ADR-008](ADR-008-firestore-deny-all.md) (Security Rules deny-all).

## Data
2026-11-01

---

## Contexto

O Caderno foi escrito para o plano **Blaze** do Firebase, que exige conta de faturamento. Sem o voucher do GCP, essa conta teria que usar o cartão de algum estudante. O laboratório publicou o **"Plano Custo Zero — sem o voucher do GCP"** (14/10/2026), que define a alternativa e pede que cada equipe registre a decisão em ADR até o Checkpoint 2.

No plano **Spark** (sem cartão) o que é Firebase "puro" continua grátis e o que é Google Cloud deixa de existir:

| Serviço | Funciona no Spark? | Limite gratuito (conferido pelo laboratório em 14/10/2026) |
| :--- | :--- | :--- |
| Firebase Hosting | Sim | 10 GB armazenados e 360 MB transferidos por dia |
| Firebase Auth (e-mail/senha e Google) | Sim | 50 mil usuários ativos por mês |
| Firestore (database padrão) | Sim | 1 GiB armazenado; 50 mil leituras, 20 mil escritas e 20 mil exclusões por dia |
| Cloud Run, Cloud Functions, Cloud Storage, Cloud Scheduler, Secret Manager, Artifact Registry, Cloud Logging, Cloud Build, alertas de orçamento | Não | — |

No Spark não há como haver cobrança: quando um limite diário estoura, o serviço para até o dia seguinte.

---

## Decisão

O Alçadas adota o **Plano Custo Zero**. Para o nosso projeto só a base comum é necessária (sem Storage e sem jobs agendados):

| Peça | Antes (Caderno) | Agora |
| :--- | :--- | :--- |
| Frontend Angular | Firebase Hosting | **Firebase Hosting** (sem mudança), com canal de preview no PR |
| Login e banco | Firebase Auth + Firestore | **Firebase Auth + Firestore no plano Spark** (sem mudança de código) |
| API NestJS | Cloud Run | **Render, web service gratuito**, a partir do Dockerfile da Semana 5 |
| Rewrite `/api/**` do Hosting | Mesma origem | **Chamada direta do front para a URL do Render**; a API libera **CORS só para o domínio `.web.app`/`.firebaseapp.com` da equipe** |
| Segredos | Secret Manager | **Secrets do GitHub e variáveis de ambiente do Render**; no Git só o `.env.example` |
| Credencial da API | Workload Identity Federation | **Chave da conta de serviço do Firebase Admin** em variável protegida, com papel mínimo (`Cloud Datastore User`) — **exceção ao Caderno** |
| Imagens | Artifact Registry, retenção de 5 | **Build do Render a partir do repositório** (não há registro de imagens para limpar) |
| Deploy | Preview no PR + produção no merge | Front: igual, no Hosting. API: **deploy automático do Render no merge da `main`**, depois do CI verde. O backend não tem preview; no PR ele só roda os testes |
| Logs e alertas | Cloud Logging / Monitoring | **Logs JSON no Render**; **health check do Render em `/api/healthz`**; **UptimeRobot em `/api/readyz`** com alerta por e-mail |
| Rollback | `gcloud run services update-traffic` | **Painel do Render** (2 deploys anteriores) e histórico do Hosting |
| Orçamento | Alertas 50/80/100 % | **Print de cada serviço no plano gratuito, sem forma de pagamento**, em [`/docs/finops`](../../finops/index.md) |
| Exportação da trilha (US-13) | — | CSV/JSON gerado na API e baixado direto, sem guardar arquivo |

**Regras de ouro** (fiscalizadas pelo DevOps): nunca cadastrar cartão em nenhum serviço (Google Cloud, Firebase, Render, GitHub, UptimeRobot); nunca clicar em "Upgrade"/"Blaze" nem vincular conta de faturamento; testes pesados (carga com k6, testes automatizados, seed grande) **só no emulador**; um projeto Firebase da equipe, compartilhado como membro; `gitleaks` obrigatório no CI; serviço novo só com ADR e aprovação do Tech Lead.

**Meta de custo:** US$ 0,00 em todos os checkpoints (o Caderno permite até US$ 5, 12 e 40).

---

## Consequências

### Positivas
- Nenhum estudante cadastra cartão e nenhuma cobrança é possível.
- Firestore, Auth, Hosting, Security Rules, emuladores e o código da API não mudam.
- **Reversível:** se o voucher chegar, basta mover a API para o Cloud Run; Firestore, Auth e Hosting continuam.

### Negativas / Mitigações
- **Cold start de ~1 minuto** (o web service gratuito dorme após 15 min sem tráfego).
  *Mitigação:* acordar a API 5 minutos antes da demo e dos testes; a tela de "Carregando…" com retry cobre a espera (`RNF08`).
- **750 horas por mês por workspace** (um serviço ligado o mês inteiro usa ~744 h).
  *Mitigação:* um único serviço no workspace do Render da equipe.
- **Cotas diárias do Firestore** (50 mil leituras / 20 mil escritas por dia).
  *Mitigação:* paginação com `limit` ≤ 50, consultas sempre por `tenantId`, nada de seed grande em produção, testes de concorrência só no emulador e nenhuma carga pesada na véspera do Demo Day.
- **Chave de serviço** pode vazar.
  *Mitigação:* papel mínimo, chave só em variável protegida, `gitleaks` no CI e troca imediata da chave em caso de suspeita.
- **Uma instância só:** a concorrência otimista do [ADR-010](ADR-010-concorrencia-otimista-rest.md) continua necessária (reinícios e deploys).
- **Exceções ao Caderno que precisam de aprovação do professor:** API fora do Cloud Run; CORS no lugar do rewrite de mesma origem; chave de serviço no lugar do Workload Identity; UptimeRobot no lugar do Cloud Monitoring. Os alertas de erros `5xx` e de latência p95 do Caderno também ficam sem equivalente direto. Se não forem aceitas, o Checkpoint 3 pode ser reprovado.

---

## Aval do professor

**Situação:** ⏳ **pendente** — nenhuma resposta registrada até 22/11/2026. O prazo original era 01/11 e o pedido é a primeira atividade do Checkpoint 3 (`alcadas-docs#19`).

| # | Exceção ao Caderno | O que o Alçadas usa | Como o risco é coberto | Resposta |
| :-: | :--- | :--- | :--- | :-: |
| 1 | API no Cloud Run | Web service gratuito do **Render**, a partir do Dockerfile de produção (usuário não-root) | Health check do Render em `/api/healthz`; deploy só do que está na `main` | ⏳ |
| 2 | Rewrite `/api/**` de mesma origem | Chamada direta para a URL do Render | **CORS só para `https://alcadas-app.web.app` e os canais de preview do site**; nenhuma outra origem em produção | ⏳ |
| 3 | Workload Identity Federation | **Chave da conta de serviço** do Firebase Admin | Papel mínimo, chave só nos secrets do GitHub e nas variáveis do Render, `gitleaks` no CI, troca imediata em caso de suspeita | ⏳ |
| 4 | Cloud Monitoring (uptime) | **UptimeRobot** em `/api/readyz`, checagem a cada 5 min e alerta por e-mail | `/readyz` responde `503` quando o Firestore está fora ou a credencial é inválida | ⏳ |
| 5 | Alertas de `5xx` e de latência p95 > 1,5 s | Sem alerta automático gratuito equivalente | Ver a proposta abaixo | ⏳ |

**Proposta para o item 5** (levar pronta, em vez de só perguntar):

- **Erros `5xx`:** a API grava uma linha JSON por requisição, com `severity: "ERROR"` e o tipo da exceção em todo `5xx`, sem dados pessoais (`RNF09`). A indisponibilidade total já gera alerta pelo UptimeRobot (item 4). Os `5xx` pontuais são revisados no painel de logs do Render, filtrando por `"severity":"ERROR"`, na reunião de segunda-feira e antes de cada gate.
- **Latência p95:** cada linha de log traz `duracaoMs`, o que permite calcular o p95 da semana a partir dos logs do Render. O UptimeRobot também registra o tempo de resposta de `/readyz`. O valor entra no relatório da sprint, sem alerta automático.
- **Alternativa a avaliar com o professor:** trocar o UptimeRobot por outro monitor gratuito que também alerte por tempo de resposta (sugestões recebidas: Pulsetic ou Better Stack). A troca só é feita com o aval do professor e uma atualização desta ADR, e nunca com cartão cadastrado.

**Registro da resposta** (preencher quando chegar):

```markdown
Data da resposta: dd/mm/2026
Meio: e-mail / reunião / fórum (link ou print em docs/assets/images/aval-professor-adr-011.png, se a pessoa autorizar)
Exceções aceitas: 1, 2, 3, 4
Alertas de 5xx e p95: <decisão do professor>
Condições ou ressalvas: <se houver>
```

Quando a resposta chegar, atualizar a coluna **Resposta** (✅ aceita · ❌ recusada), o **Status** no topo desta ADR e o [catálogo de ADRs](index.md).

---

## Referências
- Plano Custo Zero — sem o voucher do GCP (AI Lab / Makers, 14/10/2026).
- [Render — plano gratuito](https://render.com/docs/free).
- Caderno de Projetos B2B v2, seções 1.2, 1.5, 1.8, 1.9 e 3.2.
