# Especificação OpenAPI v0.2.0

Contrato da API REST do **Alçadas** no **Checkpoint 2** (núcleo funcional rodando localmente). O arquivo fonte, validado contra o schema oficial do OpenAPI 3.0, está em [`docs/api/openapi.yaml`](openapi.yaml). As convenções estão no [ADR-009](../arquitetura/adrs/ADR-009-contrato-api-v0-2.md).

- Todas as rotas usam o prefixo **`/api`** (ex.: `http://localhost:3000/api/solicitacoes`).
- Todas exigem `Authorization: Bearer <token do Firebase Auth>`, **exceto** `/healthz` e `/readyz`.
- O tenant e o papel vêm sempre do token. Nenhuma rota recebe `tenantId` na URL ou no corpo.
- A coluna **Situação** mostra o que já está no código: ✅ no `develop` · 🔍 em PR aberto · ⏳ previsto.

---

## Histórico de versões

| Versão | Data | Mudanças |
| :--- | :--- | :--- |
| **0.2.0** | 01/11/2026 | Jornada 1 (`/centros-custo`, `/alcadas/tetos`); `/healthz` e `/readyz` públicos; `versao` obrigatório nas decisões; respostas `403`/`404`/`409` detalhadas com códigos `TRANSICAO_INVALIDA` e `CONFLITO_VERSAO`; campo `motivo` na verificação da trilha; paginação limitada a 50; servidor fictício de produção removido; extensão `x-implementado`. |
| 0.1.0 | 18/10/2026 | Contrato inicial do Checkpoint 1 (solicitações, decisão e trilha). |

---

## 1. Visão Geral dos Endpoints

| Método | Endpoint | Responsabilidade | Papel | Sucesso | Erros | Situação |
| :---: | :--- | :--- | :--- | :---: | :---: | :---: |
| `GET` | `/healthz` | Processo no ar | Público | `200` | — | 🔍 |
| `GET` | `/readyz` | API consegue falar com o Firestore | Público | `200` | `503` | 🔍 |
| `POST` | `/centros-custo` | Cadastrar centro de custo (US-01) | `ADMIN` | `201` | `400`, `401`, `403`, `409` | 🔍 `#14` |
| `GET` | `/centros-custo` | Listar centros de custo do tenant | Autenticado | `200` | `401` | 🔍 `#14` |
| `PUT` | `/alcadas/tetos` | Configurar tetos N1, N2, N3 (CA-01.1) | `ADMIN` | `200` | `400`, `401`, `403`, `404` | 🔍 `#14` |
| `GET` | `/alcadas/tetos` | Consultar tetos vigentes | Autenticado | `200` | `401` | 🔍 `#14` |
| `POST` | `/solicitacoes` | Submeter pedido (US-02, T01) | `SOLICITANTE` | `201` | `400`, `401`, `403` | 🔍 `#18` |
| `GET` | `/solicitacoes` | Listar com paginação (`page`, `limit ≤ 50`) | Autenticado | `200` | `400`, `401` | 🔍 `#18` |
| `GET` | `/solicitacoes/{id}` | Detalhe da solicitação | Autenticado do tenant | `200` | `401`, `403`, `404` | ⏳ |
| `POST` | `/solicitacoes/{id}/aprovar` | Aprovar (T02) | Aprovadores | `200` | `400`, `401`, `403`, `404`, `409` | ✅ |
| `POST` | `/solicitacoes/{id}/rejeitar` | Rejeitar com motivo ≥ 10 (T03) | Aprovadores | `200` | `400`, `401`, `403`, `404`, `409` | ✅ |
| `POST` | `/solicitacoes/{id}/ajuste` | Pedir ajuste com motivo ≥ 10 (T04) | Aprovadores | `200` | `400`, `401`, `403`, `404`, `409` | ✅ |
| `GET` | `/solicitacoes/{id}/trilha` | Elos SHA-256 da solicitação (US-07) | Aprovadores, `AUDITOR`, `ADMIN`, autor | `200` | `401`, `403`, `404` | 🔍 |
| `POST` | `/solicitacoes/{id}/trilha/verificar` | Recalcular a cadeia e apontar adulteração (US-08) | Aprovadores, `AUDITOR`, `ADMIN`, autor | `200` | `401`, `403`, `404` | 🔍 |
| `GET` | `/solicitacoes/{id}/trilha/exportar` | Exportar CSV/JSON (US-13) | `AUDITOR`, `ADMIN` | `200` | `401`, `403` | ⏳ CP3 |

**Aprovadores** = `USER_NORMAL`, `SUPER_USER`, `APROVADOR_N1`, `APROVADOR_N2`, `DIRETOR_CFO` (ver [ADR-007](../arquitetura/adrs/ADR-007-rbac-e-sod.md)). O papel libera a rota; o teto da alçada e o SoD são conferidos em seguida, no caso de uso.

**Fora do contrato v0.2 (próximas versões):** reenvio após ajuste (`T05`) e cancelamento (`T06`), delegações (`RF04`/`US-10`).

---

## 2. Detalhamento dos Endpoints Principais

### 2.1. Submissão de Solicitação (`POST /solicitacoes`)
Cria uma nova solicitação no status `PENDENTE` e gera automaticamente o **Elo Log 1** na trilha criptográfica.

- **Payload de Entrada (`CriarSolicitacaoDto`):**
  ```json
  {
    "centroCustoId": "CC-FIN-01",
    "valor": 4500.00,
    "categoria": "EQUIPAMENTOS",
    "justificativa": "Aquisição emergencial de licenças adicionais para o setor financeiro.",
    "fornecedor": "Softwares SA"
  }
  ```
- **Regras de Negócio e Erros:**
  - `400 Bad Request`: Se faltar qualquer campo obrigatório, se o valor for ≤ 0, ou se a justificativa tiver menos de 30 caracteres para valores acima da faixa base da categoria (`CA-02.2`).

---

### 2.2. Decisão de Aprovação (`POST /solicitacoes/{id}/aprovar`)
Aprova formalmente o pedido, garantindo que o valor respeite a alçada do aprovador e que não haja autoaprovação (*SoD*).

- **Payload de Entrada (`DecisaoAprovacaoDto`):**
  ```json
  {
    "versao": 1,
    "observacao": "Aprovado conforme política de compras da unidade."
  }
  ```
- **Regras de Bloqueio e Erros:**
  - `403 Forbidden`: Se `solicitanteId === aprovadorId` (Bloqueio de SoD - grava **Log 8**) ou se `valor > tetoAlcada`;
  - `409 Conflict` com `error: "TRANSICAO_INVALIDA"`: a solicitação não está `PENDENTE` (já `APROVADA`, `REJEITADA` ou `EM_AJUSTE` — `CA-03.4`);
  - `409 Conflict` com `error: "CONFLITO_VERSAO"`: `versao` ausente, desatualizada ou outro aprovador decidiu no mesmo instante (`CA-05.1`). Dois cliques simultâneos em "Aprovar" geram exatamente um `200` e um `409`.

---

### 2.3. Bloqueio / Rejeição Formal (`POST /solicitacoes/{id}/rejeitar`)
Reprova a solicitação, transferindo-a de forma irreversível para o estado terminal `REJEITADA` e gerando o **Elo Log 3**.

- **Payload de Entrada (`DecisaoRejeicaoDto`):**
  ```json
  {
    "versao": 1,
    "motivo": "Valor cotado está 40% acima do preço de referência de mercado."
  }
  ```
- **Regras de Validação:**
  - O campo `motivo` é obrigatório e exige tamanho mínimo de **10 caracteres** (`CA-03.3`). Se for menor, a API recusa com `HTTP 400 Bad Request`.

---

### 2.4. Trilha de Auditoria Criptográfica (`GET /solicitacoes/{id}/trilha`)
Retorna a cadeia de elos imutáveis calculada com SHA-256.

- **Exemplo de Retorno (Lista de Elos):**
  ```json
  [
    {
      "sequencia": 1,
      "tipoEvento": "CRIACAO_SOLICITACAO",
      "authorId": "usr-solic-01",
      "timestampUtc": "2026-10-20T18:00:00.000Z",
      "hashAnterior": "0000000000000000000000000000000000000000000000000000000000000000",
      "hashAtual": "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8",
      "payload": {
        "centroCustoId": "CC-FIN-01",
        "valor": 4500.00,
        "categoria": "EQUIPAMENTOS"
      }
    },
    {
      "sequencia": 2,
      "tipoEvento": "APROVACAO",
      "authorId": "usr-aprov-02",
      "timestampUtc": "2026-10-20T18:15:30.000Z",
      "hashAnterior": "5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8",
      "hashAtual": "a3b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1",
      "payload": {
        "cargo": "GERENTE",
        "limiteAlcada": 5000.00
      }
    }
  ]
  ```

---

### 2.5. Verificação de Integridade em Tempo Real (`POST /solicitacoes/{id}/trilha/verificar`)
Recalcula determinísticamente todos os hashes SHA-256 da cadeia em menos de 1 segundo (`RN08` / `CA-08.1`):

- **Resposta de Integridade Positiva (`200 OK`):**
  ```json
  {
    "status": "INTEGRA",
    "totalElos": 2,
    "tempoProcessamentoMs": 18.4,
    "eloInconsistente": null,
    "motivo": null,
    "mensagem": "Todos os selos criptográficos SHA-256 coincidem deterministicamente."
  }
  ```
- **Resposta em Caso de Adulteração no Banco (`200 OK`):**
  ```json
  {
    "status": "INCONSISTENTE",
    "totalElos": 2,
    "tempoProcessamentoMs": 16.1,
    "eloInconsistente": 1,
    "motivo": "HASH_DIVERGENTE",
    "mensagem": "Inconsistente / Adulterado: o elo 1 falhou na verificação (HASH_DIVERGENTE)."
  }
  ```

---

### 2.6. Configuração de tetos (`PUT /alcadas/tetos`)
Grava a matriz de alçadas de um centro de custo. Teto vazio, zero ou negativo → `400` e nada é gravado (`CA-01.1`).

```json
{ "centroCustoId": "cc-clinica-centro", "N1": 1000.00, "N2": 5000.00, "N3": null }
```

---

## 3. Padrão Global de Erros

Todas as respostas de erro da API seguem a estrutura JSON padronizada:

```json
{
  "statusCode": 403,
  "error": "Forbidden",
  "message": "SoD: O solicitante está estritamente proibido de aprovar a própria solicitação.",
  "timestamp": "2026-10-20T18:30:00.000Z"
}
```

Em respostas `400` de validação, o campo opcional `campos` lista os atributos inválidos (ex.: `["valor"]`). Em `409`, o campo `error` traz `TRANSICAO_INVALIDA` ou `CONFLITO_VERSAO`.
