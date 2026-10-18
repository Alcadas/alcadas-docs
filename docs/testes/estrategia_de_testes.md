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

A matriz liga cada requisito ao seu critério de aceite, ao teste que o verifica e ao resultado. O formato é este:

| Requisito | Critério de aceite | Teste | Resultado |
|---|---|---|---|
| RF01 / US-01 | CA-01.2: com teto do Gerente = R$ 5.000,00, um pedido de R$ 5.000,01 não pode ser decidido pelo Gerente (403) | CT-01 | Pendente |
| RF11 / US-05 | CA-05.1: duas decisões simultâneas sobre a mesma solicitação: exatamente 1 sucesso e 1 resposta 409 | CT-02 | Pendente |
| RNF07 / US-15 | CA-16.1: em acessos cruzados entre tenants, a resposta é 403 ou 404 | CT-TENANT-01 | Pendente |

O resultado pode ser Passou, Falhou ou Pendente. Os requisitos e critérios de aceite vêm dos Requisitos e das Histórias de Usuário do projeto. A matriz completa, com um teste para cada critério, será entregue no Checkpoint 2.
