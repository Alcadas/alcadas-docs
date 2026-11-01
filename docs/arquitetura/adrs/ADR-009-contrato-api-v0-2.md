# ADR-009: Convenções do Contrato da API (OpenAPI v0.2.0)

## Status
**Aceito** (Checkpoint 2)

## Data
2026-11-01

---

## Contexto

Na revisão do contrato para o Checkpoint 2 (issue `alcadas-docs#15`) apareceram três divergências entre documentos e código:

1. A [matriz de rastreabilidade](../../rastreabilidade.md) usava rotas em inglês com versão no caminho (`/api/v1/requests/{id}/approve`, `/tenants/{tId}/cost-centers`), enquanto o [OpenAPI v0.1](../../api/index.md) e a API implementada usam português (`/api/solicitacoes/{id}/aprovar`).
2. O PR de configuração de alçadas (`alcadas-core#14`) expõe cada operação duas vezes — `/centros-custo` e `/tenants/:tenantId/cost-centers`, `PUT` e `POST /alcadas/tetos` — e devolve as entidades com os nomes de campo da persistência (`code`, `name`, `maxAmount`).
3. O contrato v0.1 não dizia de onde vem o tenant nem quais rotas são públicas.

Sem uma regra única, o teste de contrato do QA não tem referência estável e o frontend precisa tratar dois formatos.

---

## Decisão

1. **Idioma:** rotas e campos JSON da API em **português**, iguais aos do domínio (`solicitacoes`, `centros-custo`, `alcadas/tetos`, `valor`, `teto`). A persistência no Firestore continua com os nomes do [Modelo de Dados](../modelo-dados.md) (inglês); o mapeamento é feito na camada de API.
2. **Prefixo e versão:** todas as rotas ficam sob `/api` (prefixo global do NestJS). A versão do contrato fica em `info.version` (SemVer), **não** no caminho. Mudança incompatível sobe a versão menor enquanto estivermos em `0.x` e é registrada na página da API.
3. **Tenant e papel vêm do token**, nunca da URL ou do corpo. Por isso rotas com `{tenantId}` no caminho não fazem parte do contrato.
4. **Uma operação, uma rota.** Aliases (`/tenants/:tenantId/cost-centers`, `/tenants/:tenantId/tiers`, `POST /alcadas/tetos`) devem ser removidos antes do merge do `alcadas-core#14`. A rota auxiliar `POST /alcadas/validar-decisao` só entra no contrato se o frontend precisar dela.
5. **Rotas públicas:** apenas `/api/healthz` e `/api/readyz` (`security: []`). Todas as outras exigem `Authorization: Bearer <token do Firebase Auth>`.
6. **Erros:** corpo único `ErrorResponse` (`statusCode`, `error`, `message`, `timestamp`, `campos?`). A máquina de estados usa os códigos `TRANSICAO_INVALIDA` e `CONFLITO_VERSAO` no campo `error` das respostas 409.
7. **Situação de implementação:** cada operação traz `x-implementado` (`true`, `em-revisao` ou `false`), para o contrato poder ficar à frente do código sem esconder o que ainda falta.

---

## Consequências

### Positivas
- Uma referência só para frontend, backend e testes de contrato.
- O tenant nunca é confiado ao cliente, o que fecha uma classe de falhas de isolamento (`RNF07`).

### Negativas / Mitigações
- O `alcadas-core#14` precisa mapear a resposta para os campos em português (`codigo`, `nome`, `teto`, `semLimite`…) e remover os aliases.
  *Mitigação:* a entrada já aceita os nomes em português; a mudança fica só na saída.
- Rotas planejadas (`GET /solicitacoes/{id}`, `/trilha/exportar`, reenvio T05 e delegações) ainda não estão implementadas.
  *Mitigação:* marcadas como `x-implementado: false` no contrato e listadas na página da API.
