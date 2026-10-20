# Especificação OpenAPI v0.1

Esta página apresenta a especificação formal da API REST do **Alçadas** para o **Checkpoint 1**, conforme estabelecido no **Guia do Checkpoint 1**.

A API documenta os contratos de entrada, saída, códigos de status e tratamento de erros das operações essenciais: submissão de pedidos, aprovação, bloqueio/rejeição/ajuste, consulta isolada por tenant e trilha de auditoria criptográfica.

O arquivo fonte validado em formato OpenAPI 3.0.3 está disponível em: [`docs/api/openapi.yaml`](openapi.yaml).

---

## 1. Visão Geral dos Endpoints

| Método | Endpoint | Responsabilidade | Papel Mínimo | Respostas de Sucesso | Erros Mapeados |
| :---: | :--- | :--- | :---: | :---: | :---: |
| `POST` | `/solicitacoes` | Submissão de novo pedido de compra/reembolso | `SOLICITANTE` | `201 Created` | `400`, `401`, `403` |
| `GET` | `/solicitacoes` | Listagem com isolamento estrito de tenant | Qualquer autenticado | `200 OK` | `401` |
| `GET` | `/solicitacoes/{id}` | Consulta detalhada de uma solicitação | Usuário do Tenant | `200 OK` | `401`, `403`, `404` |
| `POST` | `/solicitacoes/{id}/aprovar` | Parecer favorável e avanço para `APROVADA` | `USER_NORMAL` / `SUPER_USER` | `200 OK` | `400`, `401`, `403`, `409` |
| `POST` | `/solicitacoes/{id}/rejeitar` | Reprovação formal com justificativa (≥ 10 chars) | `USER_NORMAL` / `SUPER_USER` | `200 OK` | `400`, `401`, `403`, `409` |
| `POST` | `/solicitacoes/{id}/ajuste` | Devolução para complementação pelo solicitante | `USER_NORMAL` / `SUPER_USER` | `200 OK` | `400`, `401`, `403`, `409` |
| `GET` | `/solicitacoes/{id}/trilha` | Consulta dos elos e selos SHA-256 encadeados | Usuário do Tenant | `200 OK` | `401`, `403`, `404` |
| `POST` | `/solicitacoes/{id}/trilha/verificar` | Verificação determinística da higidez dos hashes | Auditor / Admin | `200 OK` | `401`, `403`, `404` |
| `GET` | `/solicitacoes/{id}/trilha/exportar` | Exportação auditável da cadeia em CSV ou JSON | Auditor / Admin | `200 OK` | `401`, `403` |

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
  - `409 Conflict`: Se a solicitação já se encontrar em estado terminal (`APROVADA` ou `REJEITADA`), ou se a versão fornecida divergir da versão persistida (outro aprovador decidiu simultaneamente - `CA-05.1`).

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
    "mensagem": "Divergência detectada no recálculo do elo 1: o hash armazenado não confere com os dados atuais do registro."
  }
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
