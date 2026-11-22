# ADR-012: Contrato da API v0.3.0 — Produção no Render e Rotas de Plataforma

## Status
**Proposto** (Checkpoint 3) — **complementa o [ADR-009](ADR-009-contrato-api-v0-2.md)**, abrindo uma exceção ao item 3 (tenant sempre do token) só para as rotas de plataforma `saas/tenants`.

## Data
2026-11-22

---

## Contexto

O [ADR-009](ADR-009-contrato-api-v0-2.md) decidiu que o tenant vem sempre do token e que rotas com `{tenantId}` no caminho não entram no contrato. A regra vale para as jornadas de uma empresa, em que o usuário só enxerga os próprios dados.

No Checkpoint 3 a API passou a expor a administração das empresas clientes (`alcadas-core#28`). O `SUPER_USER` da plataforma não pertence a nenhuma empresa e precisa dizer **qual** empresa está criando ou alterando, então o identificador tem de vir da requisição. Ao mesmo tempo, a API foi publicada no Render ([ADR-011](ADR-011-hospedagem-render-firebase-spark.md)) e ganhou logs JSON com `requestId` (`alcadas-core#35`), e o contrato v0.2.0 ainda só listava o servidor local.

---

## Decisão

1. **Servidor de produção no contrato:** `https://alcadas-api.onrender.com/api` passa a ser o primeiro servidor do `openapi.yaml`, com o servidor local logo depois. A versão sobe para **0.3.0**.
2. **Exceção ao item 3 do ADR-009, só para a plataforma:** as rotas `GET /saas/tenants`, `PUT /saas/tenants/{tenantId}` e `POST /saas/tenants/sync` entram no contrato com a tag **Plataforma**. O `{tenantId}` do caminho nunca é confiado sozinho:
    - `GET` e `POST sync` são exclusivas do `SUPER_USER`;
    - no `PUT`, o `ADMIN` só é aceito quando o `tenantId` do caminho é igual ao do token. Fora isso a resposta é `403`;
    - `platform-saas` é reservado e qualquer `tenantId` fora de `^[A-Za-z0-9_-]+$` responde `400`.
3. **Campos da plataforma protegidos:** `plan`, `status`, `maxUsers` e `createdAt` só mudam pelo `SUPER_USER`. Quando o `ADMIN` salva, a API mantém os valores já gravados.
4. **Rastreabilidade:** toda resposta traz o cabeçalho `X-Request-Id`, o mesmo valor da linha de log da requisição. Um valor enviado pelo cliente é reaproveitado quando tem formato seguro.
5. **Saúde:** `/healthz` devolve o SHA do commit publicado quando o ambiente o informa. `/readyz` documenta o `503` com `error: NAO_PRONTO` e o reaproveitamento do resultado saudável por 60 s.

---

## Consequências

### Positivas
- O contrato descreve a API que está publicada, e o frontend e os testes de contrato podem apontar para a URL de produção.
- A exceção fica restrita a três rotas, com a regra de acesso escrita no contrato e coberta pelos testes de autorização do `alcadas-core#28`.
- Um erro relatado por um usuário pode ser achado no log do Render pelo `X-Request-Id`, sem gravar dado pessoal (`RNF09`).

### Negativas / Mitigações
- **Mais de um caminho até os dados de uma empresa.** Um erro no `RolesGuard` exporia empresas inteiras.
  *Mitigação:* papel lido só das claims do token verificado (o cabeçalho `x-user-role` é ignorado) e testes que cobrem 401, 403 e acesso cruzado entre empresas.
- **`GET /saas/tenants` lê a coleção inteira** (uma leitura por empresa), contrariando a paginação do ADR-009.
  *Mitigação:* aceitável enquanto houver poucas empresas; a revisão de leituras do Firestore ([FinOps](../../finops/index.md)) acompanha o custo.
- **Rotas da API ainda fora do contrato:** `PUT /solicitacoes/{id}`, `POST /alcadas/tetos`, `POST /alcadas/validar-decisao`, `organizacao/fluxos` e os aliases `tenants/{tenantId}/cost-centers` e `tenants/{tenantId}/tiers`, que o ADR-009 mandava remover.
  *Mitigação:* listadas na [página do contrato](../../api/index.md). Devem ser descritas ou removidas da API antes da v1.0.

---

## Referências
- [OpenAPI v0.3.0](../../api/index.md) e `docs/api/openapi.yaml`.
- `alcadas-core#28` (RBAC em `saas/tenants`), `#29` (`/readyz` no Firestore real), `#35` (logs JSON e `X-Request-Id`) e `#92` (BUG-002, `GET /solicitacoes/{id}`).
