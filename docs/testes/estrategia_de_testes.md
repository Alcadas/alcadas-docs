# Estratégia de Testes — Alçada

Status: rascunho (Checkpoint 1)
Responsável: QA, com apoio do Tech Lead
Referências: Checkpoints e Pontos de Controle B2B, Guia do Checkpoint 1, Visão do Produto, Requisitos e Histórias de Usuário

## 1. Objetivo

Garantir que o Alçada faça o que promete: toda aprovação com dono, limite e trilha. Os testes cobrem as cinco jornadas do MVP (configurar alçadas, criar solicitação, decidir, sinalizar riscos e consultar a trilha de auditoria).

Três pontos recebem atenção especial por serem os maiores riscos do projeto:

- duas pessoas decidindo a mesma solicitação ao mesmo tempo;
- uma empresa conseguindo ver dados de outra;
- a trilha de auditoria ser alterada sem ninguém perceber.

## 2. Níveis de teste

| Nível | O que verifica |
|---|---|
| Unitário e EP/BVA | Regras isoladas (alçadas, estados, riscos), com valores de teste escolhidos por EP e BVA |
| Contrato de API | Respostas conformes ao OpenAPI |
| Integração | API, frontend e emuladores funcionando juntos |
| Autorização | Sem token, token inválido ou expirado, papel errado e tenant errado |
| Falhas e timeouts | Serviço fora do ar, resposta lenta, dados malformados |
| Carga leve | Tempo de resposta sob uso repetido |
| Smoke E2E | Jornada principal funcionando depois do deploy |

## 3. EP e BVA

EP (partição de equivalência) divide as entradas em grupos que o sistema trata do mesmo jeito, e testa um valor de cada grupo. BVA (valor limite) testa nas bordas de cada grupo, onde os erros mais aparecem.

Exemplo 1, a partir dos critérios CA-01.2 e CA-06.3, com teto do cargo Gerente = R$ 5.000,00:

| Valor do pedido | Resultado esperado |
|---|---|
| Até R$ 5.000,00 | O Gerente pode decidir |
| Acima de R$ 5.000,00 | O Gerente recebe 403 e o pedido vai ao nível superior |
| Zero, negativo ou vazio | Recusado com mensagem de erro |

Valores de borda a testar: 0,00; 0,01; 4.999,99; 5.000,00; 5.000,01.

Exemplo 2, a partir do critério CA-06.2 (janela móvel de 30 dias do fracionamento):

| Pedido anterior feito há | Resultado esperado |
|---|---|
| 30 dias ou menos | Soma considerada; pode bloquear |
| 31 dias ou mais | Fora da janela; sem bloqueio |

Valores de borda a testar: 29, 30 e 31 dias.

## 4. Ferramentas

A stack está definida na Visão do Produto: Angular (frontend), NestJS (backend), Firestore e Firebase Auth, em um monorepo Nx (`alcadas-core`). As ferramentas de teste seguem essa stack.

| Finalidade | Ferramenta |
|---|---|
| Testes unitários, contrato, integração e autorização (backend) | Jest |
| Testes de API de ponta a ponta | Jest, em `apps/api-e2e` |
| Testes do frontend | Jest |
| Ambiente local | Docker Compose com emuladores do Firebase |
| Acessibilidade | Lighthouse |
| Execução automática | GitHub Actions |
| Relatórios | JUnit e cobertura gerados no CI, em `/docs/testes` |

## 5. Matriz de rastreabilidade

A matriz liga cada requisito ao seu critério de aceite, ao teste que o verifica e ao resultado:

| Requisito | Critério de aceite | Teste | Tipo | Resultado |
|---|---|---|---|---|
| RNF05 / Base | Inicialização da API e resposta do endpoint raiz | CT-SMOKE-01 | Integração (E2E) | **Passou** |
| RNF05 / Base | Compilação e build sem erros no monorepo Nx (`web`, `api`, `models`) | CT-BUILD-01 | Build / CI | **Passou** |
| RNF05 / Base | Execução de testes unitários dos apps (`api`, `web`, `models`) | CT-UNIT-01 | Unitário | **Passou** |
| RNF07 / US-15 | CA-16.1: em acessos cruzados entre tenants, a resposta é 403 ou 404 | CT-TENANT-01 | Integração (E2E) | **Pendente (Estruturado)** |
| RF01 / US-01 | CA-01.2: com teto do Gerente = R$ 5.000,00, um pedido de R$ 5.000,01 não pode ser decidido pelo Gerente (403) | CT-01 | Unitário / Regra | Pendente |
| RF11 / US-05 | CA-05.1: duas decisões simultâneas sobre a mesma solicitação: exatamente 1 sucesso e 1 resposta 409 | CT-02 | Concorrência | Pendente |

O resultado pode ser **Passou**, **Falhou** ou **Pendente**. Os requisitos e critérios de aceite vêm dos [Requisitos](../requisitos.md) e das [Histórias de Usuário](../historias-usuario.md) do projeto. A matriz completa, com um teste para cada critério, será entregue no Checkpoint 2.

## 6. Evidências dos Testes de Integração (Checkpoint 1)

Durante a validação técnica do Checkpoint 1, foram executados e homologados os seguintes testes no monorepo `alcadas-core`:

1. **Teste de Integração de API (`apps/api-e2e`):**
   - Execução do comando `npx nx run api-e2e:e2e`;
   - O orquestrador subiu a instância real da API NestJS na porta 3000, realizou a requisição HTTP GET para `/api`, validou o status code `200 OK` e o payload `{ message: 'Hello API' }`, e encerrou o processo liberando a porta com sucesso (tempo total de execução: 0.434s na suíte Jest);
   - Resultado: **100% dos testes de integração de base passaram com sucesso**.

2. **Isolamento de Tenants (`CT-TENANT-01`):**
   - Estruturado na branch `test/#4-isolamento-tenant` através da suíte `apps/api-e2e/src/api/tenant-isolation.spec.ts`;
   - Contém os dois cenários centrais:
     - *Cenário A:* Usuário da empresa A recebe 403 ou 404 ao tentar consultar solicitação da empresa B (`GET /api/solicitacoes/:id`);
     - *Cenário B:* Listagem da empresa A não retorna solicitações da empresa B (`GET /api/solicitacoes`).
   - Conforme previsto para o Checkpoint 1, os testes encontram-se temporariamente marcados com `it.skip` para ativação assim que as rotas de negócio forem conectadas à base no Checkpoint 2.

