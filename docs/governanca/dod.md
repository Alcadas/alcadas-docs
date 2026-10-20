# Definition of Done (DoD) — Definição de Pronto

A **Definition of Done (DoD)** estabelece os critérios objetivos de qualidade, conformidade técnica e completude que **toda entrega de funcionalidade, refatoração ou correção de defeito deve cumprir obrigatoriamente** antes de ser considerada concluída e aceita no repositório principal (`develop` / `main`).

Este documento está em estrita conformidade com o **Caderno B2B v2 (Seção 1.6 — Definition of Done)** e norteia o processo de desenvolvimento e homologação do sistema **Alçadas**.

---

## 1. Os 6 Itens Mínimos Obrigatórios do Caderno B2B (Seção 1.6)

Para que qualquer *Pull Request (PR)* ou item do backlog seja dado como *Pronto (Done)*, ele deve atender comprovadamente aos seis pilares abaixo:

### 1. Código Funcional e Testes Automatizados
- O código implementa integralmente os fluxos previstos na estória/tarefa sem comportamentos pendentes;
- Testes unitários para regras de negócio (limites de alçada, cálculo anti-fracionamento, máquina de estados, hashes SHA-256) foram escritos e passam com sucesso;
- Testes de integração entre API e base de dados/emuladores validam os contratos de ponta a ponta (`apps/api-e2e`);
- **Comando de validação local:** `npm run test` (todos os testes verdes).

### 2. Cobertura de Testes e Critérios de Aceite das User Stories
- Todos os Critérios de Aceite (CAs) vinculados à respectiva *User Story* foram explicitamente verificados e possuem evidências ou asserções automatizadas;
- A cobertura de código não pode sofrer regressão em relação à baseline do projeto;
- Valores de borda e partições de equivalência (EP/BVA) foram testados conforme a [Estratégia de Testes](../testes/estrategia_de_testes.md).

### 3. Build sem Erros e CI Verde no GitHub Actions
- A aplicação compila sem avisos impeditivos ou erros de compilação TypeScript nos três projetos do monorepo (`web`, `api`, `models`);
- A pipeline de Integração Contínua (CI) do GitHub Actions executa e conclui com status **100% verde (SUCCESS)** em todos os jobs da branch do PR antes da aprovação;
- **Comando de validação local:** `npm run build`.

### 4. Padrões de Código, Linter e Formatação
- O código está em conformidade com as regras de estilo definidas no `.editorconfig`, ESLint e Prettier;
- Não há variáveis órfãs, tipos `any` injustificados ou violações de arquitetura limpa;
- Ausência total de segredos de autenticação, senhas ou dados pessoais identificáveis (PII) em código ou logs (`RNF09`);
- **Comando de validação local:** `npm run lint` e `npm run format:check`.

### 5. Documentação Técnica Atualizada
- Qualquer nova rota ou alteração de contrato foi refletida na especificação [OpenAPI v0.1](../api/index.md) (`docs/api/openapi.yaml`);
- Novas decisões arquiteturais ou mudanças em persistência/segurança possuem o respectivo [ADR](../arquitetura/adrs/index.md) documentado;
- O `README.md` e os guias de contribuição estão atualizados com eventuais novos comandos ou dependências.

### 6. Revisão de Código por Pares (Peer Review)
- Todo Pull Request deve ser avaliado e aprovado formalmente por no mínimo **um revisor par** ou pelo **Tech Lead / QA Lead** antes da mesclagem;
- O revisor deve validar a clareza do código, a fidelidade aos requisitos de negócio e a ausência de vulnerabilidades de segurança;
- Nenhuma mesclagem direta na branch `main` ou `develop` é permitida sem PR aprovado.

---

## 2. Critérios Adicionais Específicos do Projeto Alçadas

Além dos seis itens gerais do Caderno B2B, a equipe do **Alçadas** estabelece os seguintes critérios inegociáveis para o Checkpoint 1 e subsequentes:

| Requisito / Área | Critério Inegociável da DoD |
| :--- | :--- |
| **Segregação de Funções (SoD)** | Toda alteração de estado deve comprovar que solicitantes são impedidos de autoaprovar (`HTTP 403`), mesmo sob delegação (`RF03`, `RN03`, `RN12`). |
| **Isolamento de Tenants (`RNF07`)** | Toda consulta à API deve garantir isolamento de dados entre empresas distintas, retornando `403` ou `404` em acessos cruzados. |
| **Estados Visuais de Tela (`RNF08`)** | Toda tela ou componente da interface web Angular deve tratar explicitamente os três estados visuais: *Carregando (loading)*, *Vazio (empty)* e *Erro com retry (error)*. |
| **Execução Local em Emuladores (`RNF10`)** | Todas as funcionalidades devem ser executáveis e testáveis localmente utilizando os emuladores do Firebase via `docker compose up`, com custo de nuvem zero em desenvolvimento. |
| **Controle de Nuvem (`RNF06`)** | Nenhuma alteração pode introduzir recursos de nuvem que violem o teto orçamentário de US$ 50,00 na GCP. |

---

## 3. Rastreabilidade com o Template de Pull Request

Para assegurar que nenhum item da DoD seja esquecido pelos desenvolvedores, todos os 6 itens mínimos do Caderno B2B e os critérios do projeto estão incorporados como caixas de seleção obrigatórias no template de submissão do GitHub:

👉 [Visualizar Template de Pull Request](https://github.com/Alcadas/alcadas-docs/blob/main/.github/PULL_REQUEST_TEMPLATE.md)
