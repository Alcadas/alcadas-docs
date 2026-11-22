# Leituras e Escritas do Firestore por Jornada

O plano **Spark** do Firestore permite **50 mil leituras e 20 mil escritas por dia**. Quando um limite estoura, o banco para de responder até o dia seguinte e a API devolve `503` ([ADR-011](../arquitetura/adrs/ADR-011-hospedagem-render-firebase-spark.md)). Esta página estima quanto cada jornada gasta e aponta o que precisa mudar antes do Demo Day.

> [!WARNING] Estimativa pelo código, ainda não medida
> Os números abaixo foram contados lendo o código da API e do frontend na `develop` do `alcadas-core` (commit `89ca448`, 22/11/2026). Eles mostram a ordem de grandeza, mas **ainda não são medições reais**. A coluna "Medido" e o print do console devem ser preenchidos depois de um teste em produção (ver a [seção 5](#5-como-medir-em-producao)).

---

## 1. Como o Firestore cobra

- **Leitura:** cada documento devolvido conta 1. Uma consulta que não devolve nada conta 1.
- **`offset()` não economiza:** os documentos pulados também são cobrados. A página 2 com `limit=50` custa 100 leituras.
- **Escrita:** cada `set`, `create`, `update` ou `delete` conta 1, inclusive dentro de lote ou transação.
- **Transação:** o `get` feito dentro dela conta como leitura.
- **Login:** validar o token (`verifyIdToken`) não lê o Firestore.

Símbolos usados nas tabelas:

| Símbolo | Significado | Valor no seed de produção |
| :-: | :--- | :-: |
| `S` | Solicitações devolvidas na página (no máximo 50) | 0 no início, até 50 |
| `C` | Centros de custo do tenant | 3 |
| `T` | Faixas de alçada do tenant (`approval_tiers`) | 3 |
| `E` | Elos já gravados na trilha da solicitação | 1 a 4 |
| `W` | Fluxos customizados do tenant (`workflows`) | 0 |

---

## 2. Consumo por jornada

| Jornada | Rotas chamadas | Leituras | Escritas | Com o seed (50 solicitações) | Medido | Risco |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Abrir a tela de Solicitações ou Aprovações** | `GET /solicitacoes?limit=50`, `GET /centros-custo`, `GET /alcadas/tetos` | `S + C + T` | 0 | 56 leituras | _a medir_ | Médio |
| **Atualização automática** (a cada 8 s com a aba visível) | `GET /solicitacoes?limit=50`, `GET /centros-custo` | `S + C` por ciclo | 0 | 53 por ciclo · **~23.850 por hora** | _a medir_ | **Crítico** |
| **Criar solicitação** (Jornada 2) | `POST /solicitacoes` | `W` (só sem `fluxoId`; mínimo 1) | 2 (solicitação + elo gênese, em lote) | 0 ou 1 leitura · 2 escritas | _a medir_ | Baixo |
| **Aprovar, rejeitar ou pedir ajuste** (Jornada 3) | `POST /solicitacoes/{id}/aprovar` etc. | `1 + T + 1 + E` | 2 (status em transação + novo elo) | 6 leituras · 2 escritas | _a medir_ | Baixo |
| **Ver a trilha** (Jornada 5) | `GET /solicitacoes/{id}/trilha` | `1 + E` | 0 | 2 a 5 leituras | _a medir_ | Baixo |
| **Verificar a cadeia SHA-256** (Jornada 5) | `POST /solicitacoes/{id}/trilha/verificar` | `1 + E` | 0 | 2 a 5 leituras | _a medir_ | Baixo |
| **Página 2 da listagem** | `GET /solicitacoes?page=2&limit=50` | `2 × 50` (o `offset` cobra a página 1) | 0 | 100 leituras | _a medir_ | Médio |
| **Salvar a empresa** (tela de Organização) | `PUT /saas/tenants/{tenantId}` | `T` (+1 quando é o `ADMIN`) | `2 + departamentos` (+3 faixas na 1ª vez) | 4 leituras · 5 escritas | _a medir_ | Baixo |
| **Listar empresas** (`SUPER_USER`) | `GET /saas/tenants` | 1 por empresa | 0 | 1 leitura | _a medir_ | Baixo hoje |
| **Monitoramento** | `/api/readyz` (UptimeRobot a cada 5 min) | 1 a cada 60 s no máximo | 0 | **288 por dia** | _a medir_ | Baixo |
| **Health check do Render** | `/api/healthz` | 0 | 0 | 0 | — | Nenhum |

Onde cada número vem do código:

- **Listagem:** `FirestoreSolicitacoesRepository.listarPorTenant` usa `orderBy('createdAt','desc').offset(...).limit(50)`.
- **Decisão:** `MaquinaEstadosService.buscarNoTenant` lê 1 documento, ou 2 se a solicitação estiver na coleção antiga `solicitacoes`. `assertApproverWithinCeiling` lê todas as faixas. `updateTenantDocumentIfVersion` faz 1 leitura e 1 escrita na transação. `AuditoriaService.recordEvent` chama `getAuditTrail`, que **lê a trilha inteira** só para descobrir o último elo, e grava o novo elo com `create()`.
- **Criação:** `avaliarFluxoCompativel` **lê a coleção inteira `workflows`** quando o corpo não traz `fluxoId`.
- **Atualização automática:** `RequestsService.startPolling(8000)` roda em Solicitações e em Aprovações e chama `loadSilent()` e `loadCostCenters()` a cada ciclo.

---

## 3. Cenários de um dia

| Cenário | Conta | Leituras por dia | Situação |
| :--- | :--- | :-: | :--- |
| Ensaio com 1 pessoa, 1 hora com a tela aberta, 20 decisões | 23.850 + 20 × 6 + abertura de telas | ~24 mil | Cabe (48% da cota) |
| **Demo Day: 5 pessoas, 2 horas com a tela aberta** | 5 × 2 × 23.850 | **~238 mil** | **Estoura a cota ~4,8 vezes**; o banco para no meio da apresentação |
| Teste de usuário externo: 3 pessoas, 30 min cada | 3 × 0,5 × 23.850 | ~36 mil | Cabe, mas sobra pouco para o resto do dia |
| Mesmo Demo Day **depois dos ajustes 1 e 2** | 5 × 2 × 120 ciclos × ~1 leitura | ~1,2 mil | Cabe com folga |

As **escritas** não preocupam: cada decisão grava 2 documentos, então 20 mil escritas equivalem a ~10 mil decisões por dia.

---

## 4. Ajustes necessários (a abrir como issues no `alcadas-core`)

| # | Ajuste | Onde | Ganho estimado | Prioridade |
| :-: | :--- | :--- | :--- | :-: |
| 1 | **Atualização automática mais barata:** intervalo de 8 s para 30 s, parar de recarregar os centros de custo a cada ciclo e pedir só o que mudou (`where('updatedAt', '>', ultimaLeitura).limit(50)`); um ciclo sem novidade passa a custar 1 leitura | `apps/web` `RequestsService.startPolling` + filtro `desde` em `GET /solicitacoes` | ~23.850 → ~120 leituras por hora por aba | **Antes do Demo Day** |
| 2 | **Paginação por cursor** no lugar de `offset`: `startAfter(ultimoCreatedAt)` | `FirestoreSolicitacoesRepository.listarPorTenant` | Página `n` custa 50 em vez de `50 × n` | Alta |
| 3 | **Último elo sem ler a trilha toda:** `orderBy('sequencia','desc').limit(1)` em `recordEvent`; a leitura completa fica só em ver/verificar a trilha | `AuditoriaService.getAuditTrail` / `recordEvent` | `E` → 1 leitura por decisão | Média |
| 4 | **Fluxo ativo por consulta:** `where('status','==','ACTIVE').limit(1)` no lugar de `listTenantCollection('workflows')` | `SolicitacoesService.avaliarFluxoCompativel` | `W` → 1 leitura por criação | Média |
| 5 | **Faixas de alçada em cache** por 60 s, como já é feito no `/readyz` | `DecisoesService.assertApproverWithinCeiling` e `AlcadasService.getApprovalTiers` | `T` → ~0 por decisão | Baixa |
| 6 | **`GET /saas/tenants` paginado** quando houver mais empresas | `SaasController.listSaasTenants` | Proporcional ao número de empresas | Baixa |

Os trechos do `listTenantCollection` e do `getAuditTrail` lembrados na issue são os ajustes 3 e 4. O ajuste 1 é o único que coloca o Demo Day em risco.

Exemplo do ajuste 3:

```ts
// hoje: lê todos os elos para achar o último
const existingBlocks = await this.getAuditTrail(tenantId, requestId);

// proposto: lê só o último elo
const ultimo = await db
  .collection(`tenants/${tenantId}/requests/${requestId}/audit_blocks`)
  .orderBy('sequencia', 'desc')
  .limit(1)
  .get();
```

---

## 5. Como medir em produção

1. Escolher um horário sem outros testes e anotar o contador de **Leituras** e **Escritas** em Console do Firebase → Firestore → **Uso**.
2. Executar uma jornada de cada vez, com uma só pessoa e uma só aba: abrir Solicitações, esperar 1 minuto parado, criar 1 solicitação, aprovar, abrir a trilha e verificar.
3. Anotar os contadores depois de cada passo, preencher a coluna **Medido** da [seção 2](#2-consumo-por-jornada) e salvar o print como `docs/finops/cp3-firestore-uso.png` (evidência 2 da [página de FinOps](index.md)).
4. O console agrega o uso por alguns minutos; esperar a curva estabilizar antes de passar para o próximo passo.

Testes de carga e testes automatizados nunca rodam contra o Firestore real ([regra de ouro 3](index.md#3-regras-de-ouro)).
