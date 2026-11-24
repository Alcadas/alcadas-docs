# Relatório de Testes v1 

Primeiro relatório de execução da suíte do `alcadas-core`, entregue no **Checkpoint 2** (Qualidade). Ele mostra quantos testes rodaram, quantos passaram, a cobertura e os bugs abertos. A [Estratégia de Testes](estrategia_de_testes.md) define o que é testado; a [Matriz de Rastreabilidade](../rastreabilidade.md) liga cada critério de aceite ao seu caso de teste.

## 1. Onde e como rodou

| Item | Valor |
| :--- | :--- |
| Data da execução | 24/11/2026 |
| Código testado | `alcadas-core`, branch `main`, commit `0928e68` (merge do PR #104) |
| Ambiente | Local (Windows 11), Node 24, Java 21, emuladores do Firebase com o projeto `demo-alcadas` e o seed `scripts/seed.ts` |
| Unitários e cobertura | Jest de cada projeto com `--coverage`, usando o mesmo `jest.config.cts` do alvo `test` do Nx (o que o `npm run test:coverage` executa) |
| Testes de API | `npx firebase-tools emulators:exec --project demo-alcadas "npx nx e2e api-e2e"` (mesmo comando do CI) |
| Testes de tela | `npx firebase-tools emulators:exec --project demo-alcadas "npx nx e2e web-e2e"` (Playwright, Chromium; mesmo comando do CI) |

Os testes de API e de tela rodam com os emuladores ligados, como no CI. O `npm run test:e2e` sozinho não liga os emuladores, e sem eles o seed não é carregado.

## 2. Resultado geral

| Tipo | Total | Passaram | Falharam | Pendentes (`todo`) |
| :--- | ---: | ---: | ---: | ---: |
| Unitários — `apps/api` | 377 | 377 | 0 | 0 |
| Unitários — `apps/web` | 274 | 274 | 0 | 0 |
| Unitários — `libs/shared/models` | 20 | 20 | 0 | 0 |
| EP/BVA (`ep-bva.spec.ts`) | 28 | 27 | 0 | 1 |
| Critérios prioritários (`criterios-prioritarios.spec.ts`) | 7 | 6 | 0 | 1 |
| Autorização (`autorizacao.spec.ts`) | 68 | 68 | 0 | 0 |
| Isolamento de tenant (`tenant-isolation.spec.ts`) | 4 | 4 | 0 | 0 |
| Contrato OpenAPI (`contrato-openapi.spec.ts`) | 22 | 20 | 0 | 2 |
| Integração (fluxos do CP2, smoke, hosting, regras do Firestore) | 14 | 14 | 0 | 0 |
| Telas — Playwright (`solicitacoes.spec.ts`) | 2 | 2 | 0 | 0 |
| **Total** | **816** | **812** | **0** | **4** |

**812 de 812 testes executados passaram.** Os 4 pendentes estão marcados como `todo` no código porque dependem de histórias que ficam para depois do CP2 (ver seção 5).

Mesmo assim, o critério **CA-01.2 não é atendido**. O teste CT-01 passa porque verifica o `403` só quando o Gerente tenta aprovar a etapa do Diretor. O PO confirmou que o `403` deve vir já na primeira tentativa do Gerente. Por isso o CT-01 conta como falha de critério e gerou o [BUG-005](https://github.com/Alcadas/alcadas-core/issues/107) (seção 4).

O grupo "Integração" soma `fluxos-integracao-cp2.spec.ts` (5), `api.spec.ts` (1, smoke da rota `/api`), `firebase-hosting.spec.ts` (5) e `firestore-rules.spec.ts` (3).

## 3. Cobertura

Cobertura dos testes unitários:

| Projeto | Linhas | Instruções | Ramos | Funções |
| :--- | ---: | ---: | ---: | ---: |
| `apps/api` | **74,64%** (936/1254) | 75,11% | 53,09% | 75,71% |
| `apps/web` | **73,26%** (3015/4115) | 71,47% | 57,96% | 66,72% |
| `libs/shared/models` | **95,74%** (45/47) | 94,11% | 79,24% | 85,71% |
| **Geral** | **73,78%** (3996/5416) | — | — | — |

A cobertura de ramos é a mais baixa (53% na API): muitos caminhos de erro, como respostas de falha do Firestore, ainda não têm teste unitário. Esses caminhos são exercitados em parte pelos testes de API, que não entram nesta medição.

## 4. Bugs

### Bugs abertos

| Bug | Descrição | Critério | Caso de teste | Situação |
| :--- | :--- | :--- | :--- | :--- |
| [BUG-005](https://github.com/Alcadas/alcadas-core/issues/107) | Gerente aprova a própria etapa de um pedido de R$ 5.000,01 (acima do teto de R$ 5.000,00) e recebe `200`; o critério pede `403` já na primeira tentativa | CA-01.2 | CT-01 ❌ | Aberto |

Além da correção na API, o CT-01 precisa voltar a esperar `403` na primeira aprovação do Gerente, como estava antes do commit `076457c`.

### Bugs encontrados no CP2 e já fechados

Os quatro bugs registrados na execução de 18/11 foram corrigidos e as issues foram fechadas em 20/11. Nesta execução, os casos de teste ligados a eles passaram.

| Bug | Descrição | Critério | Correção | Caso de teste |
| :--- | :--- | :--- | :--- | :--- |
| [BUG-001](https://github.com/Alcadas/alcadas-core/issues/86) | `POST /solicitacoes` aceitava valor zero, negativo e com mais de 2 casas decimais | CA-02.3 | `076457c` | CT-02 ✅ |
| [BUG-002](https://github.com/Alcadas/alcadas-core/issues/92) | Rota `GET /solicitacoes/{id}` não existia na API | CA-16.1 / contrato | `ee104dd` | Isolamento de tenant ✅, contrato ✅ |
| [BUG-003](https://github.com/Alcadas/alcadas-core/issues/96) | API criava solicitação sem `centroCustoId`, `valor` ou `justificativa` | CA-02.1 | `2956228` | CT-10 ✅ |
| [BUG-004](https://github.com/Alcadas/alcadas-core/issues/97) | Centro de custo novo só aparecia no formulário depois de recarregar a página | CA-01.3 | `2956228` | CT-09 ✅ |

## 5. Pontos de atenção

**Testes pendentes (`todo`):**

| Teste | Por que está pendente |
| :--- | :--- |
| CT-15 — CA-04.2 (substituto com delegação ativa não aprova o próprio pedido) | Depende da delegação (US-10), ainda não implementada |
| CT-05 — CA-06.2 (janela de 30 dias do fracionamento) | US-06, jornada 4, fica para o CP3 |
| Contrato de `PUT /solicitacoes/{id}` (reenvio após ajuste) | US-11, rota ainda fora do contrato |
| Contrato de `GET /trilha/exportar` | US-13, rota ainda não implementada |

**CT-07 (CA-05.2):** o critério pede 100 pares de decisões simultâneas, e o teste roda 20. Passou com 20; falta ampliar para 100.

## 6. Comparação com a execução anterior

| Execução | CT-01 | CT-02 | CT-09 | CT-10 | Isolamento de tenant | Contrato |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 18/11 (execução anterior) | ❌ | ❌ | ❌ | ❌ | ❌ | 17 de 20 |
| 24/11 (este relatório) | ❌ ([BUG-005](https://github.com/Alcadas/alcadas-core/issues/107)) | ✅ | ✅ | ✅ | ✅ | 20 de 20 |
