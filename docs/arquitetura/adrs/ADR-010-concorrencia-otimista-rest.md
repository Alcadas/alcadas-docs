# ADR-010: Concorrência Otimista por Precondição do Firestore (implementação do CP2)

## Status
**Aceito** (Checkpoint 2) — **complementa o [ADR-005](ADR-005-maquina-de-estados.md)**. Provisório até a migração para o Admin SDK.

## Data
2026-11-01

---

## Contexto

O [ADR-005](ADR-005-maquina-de-estados.md) definiu que cada transição roda em `firestore.runTransaction()`, conferindo o campo `versao` e gravando o elo da trilha na mesma transação.

A API do `develop` acessa o emulador do Firestore pela **API REST** (via `axios`), sem o Admin SDK, e a primeira versão da decisão do aprovador (`alcadas-core#15`) protegia a concorrência com um *lock* em memória. Esse lock se perde a cada reinício ou deploy da API (o Render reinicia o serviço gratuito quando ele dorme) e não protege contra escrita feita por outro processo ou por mais de uma instância.

O Admin SDK (`firebase-admin`) entra junto com o PR de submissão (`alcadas-core#18`), ainda em revisão.

---

## Decisão

1. Toda mudança de status passa por um único serviço, `MaquinaEstadosService`, que aplica a tabela T01–T06 da [Tabela de Transições](../tabela-transicoes.md).
2. Até o Admin SDK estar no `develop`, a escrita usa **concorrência otimista nativa do Firestore REST**:
   - a API lê o documento e guarda o `updateTime`;
   - confere `versao` informado pelo cliente == `versao` atual (senão `409 CONFLITO_VERSAO`);
   - grava com a precondição `currentDocument.updateTime=<lido>`; se outra decisão gravou antes, o Firestore responde `FAILED_PRECONDITION` e a API devolve `409 CONFLITO_VERSAO` (`CA-05.1`).
3. O campo `versao` passa a ser **obrigatório** nas decisões (`aprovar`, `rejeitar`, `ajuste`), como já dizia o contrato.
4. Cada elo da trilha é criado com a precondição `currentDocument.exists=false`: um elo existente nunca é sobrescrito; em caso de colisão a sequência é recalculada.
5. Quando o `alcadas-core#18` for integrado, o `MaquinaEstadosService` migra para `runTransaction()` com status **e** elo na mesma transação, como no ADR-005. A interface do serviço não muda.

---

## Consequências

### Positivas
- Decisão única garantida pelo próprio banco, com qualquer número de instâncias da API.
- Testável sem emulador (o fallback em memória faz a verificação e a escrita de forma síncrona).

### Negativas / Mitigações
- **Status e elo não são atômicos** nesta fase: se a gravação do elo falhar depois da mudança de status, a trilha fica sem aquele evento.
  *Mitigação:* a verificação da trilha e o histórico da solicitação expõem a lacuna; a migração do item 5 elimina o risco e é pré-requisito do Checkpoint 3.
- O fallback em memória (quando o emulador não responde) só vale para testes e desenvolvimento.
  *Mitigação:* `/readyz` responde `503` sem Firestore, então o ambiente não é considerado pronto.
