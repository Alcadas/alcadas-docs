# Regras do Repositório

Estas regras fazem a [Definition of Done](dod.md) valer na prática: **nenhum código entra na `main` sem revisão de outra pessoa e sem CI verde**. Elas valem para os dois repositórios da organização [`Alcadas`](https://github.com/Alcadas).

| Regra | `alcadas-core` | `alcadas-docs` |
| :--- | :--- | :--- |
| Template de PR com checklist da DoD | [`.github/PULL_REQUEST_TEMPLATE.md`](https://github.com/Alcadas/alcadas-core/blob/develop/.github/PULL_REQUEST_TEMPLATE.md) | [`.github/PULL_REQUEST_TEMPLATE.md`](https://github.com/Alcadas/alcadas-docs/blob/main/.github/PULL_REQUEST_TEMPLATE.md) |
| CODEOWNERS (revisor chamado automaticamente) | [`.github/CODEOWNERS`](https://github.com/Alcadas/alcadas-core/blob/develop/.github/CODEOWNERS) | [`.github/CODEOWNERS`](https://github.com/Alcadas/alcadas-docs/blob/main/.github/CODEOWNERS) |
| CI obrigatório | `Validação de Código, Testes e Build` | `MkDocs Build Check` e `Validar contrato OpenAPI` |
| Branches protegidas | `main` e `develop` | `main` |

---

## 1. Fluxo de trabalho

1. Toda tarefa nasce de uma issue.
2. Branch a partir de `develop` (core) ou `main` (docs): `feat/#<issue>-nome`, `fix/#<issue>-descricao`, `docs/#<issue>-descricao`, `chore/#<issue>-descricao`, `test/#<issue>-descricao`.
3. Commits no padrão [Conventional Commits](https://www.conventionalcommits.org/) (`feat(api): ...`).
4. PR preenchendo o template. O CODEOWNERS pede a revisão do dono da área.
5. Merge só com **1 aprovação de outra pessoa** e **CI verde**. Quem abre o PR não aprova o próprio PR.
6. `develop` → `main` por PR ao fim de cada checkpoint.

---

## 2. Proteção de branch (configuração no GitHub)

Feita por quem é **admin** da organização em **Settings → Branches → Add branch ruleset** (ou *Branch protection rule*), uma vez por branch protegida:

| Opção | Valor |
| :--- | :--- |
| Branch name pattern | `main` (e uma regra igual para `develop` no core) |
| Require a pull request before merging | ✅ |
| Required approvals | **1** |
| Dismiss stale pull request approvals when new commits are pushed | ✅ |
| Require review from Code Owners | ✅ |
| Require status checks to pass before merging | ✅ — core: `Validação de Código, Testes e Build`; docs: `MkDocs Build Check`, `Validar contrato OpenAPI` |
| Require branches to be up to date before merging | ✅ |
| Require conversation resolution before merging | ✅ |
| Do not allow bypassing the above settings | ✅ (vale também para admins) |
| Allow force pushes / Allow deletions | ❌ |

**Como conferir:** tentar `git push origin main` direto deve ser recusado com `protected branch hook declined`, e um PR com CI vermelho deve mostrar o botão de merge bloqueado.

---

## 3. O que o revisor confere

- Testes novos para a regra alterada, com o código do critério de aceite no nome.
- Contrato OpenAPI atualizado quando a rota muda ([ADR-009](../arquitetura/adrs/ADR-009-contrato-api-v0-2.md)).
- Isolamento de tenant e papel nas rotas tocadas ([ADR-007](../arquitetura/adrs/ADR-007-rbac-e-sod.md)).
- Nenhum segredo no código e nenhum dado pessoal em log.
- Nenhum recurso novo no GCP sem ADR ou aprovação do Tech Lead.
