# Guia de Contribuição - Alçadas

Obrigado por contribuir com o UnBook! Para manter a organização do código e o padrão profissional, siga as instruções abaixo.

## Padrão de Branches

Commits na main não são permitidos, você deve apenas criar branchs a partir da main.

- `feat/numero issue - nome-da-feature` — Para novas funcionalidades.
- `fix/numero issue - descricao-do-bug` — Para correção de bugs.
- `docs/numero issue - alteracao-doc` — Para mudanças em documentações.
- `chore/numero issue - task-tecnica` — Para refatorações, atualizações de libs ou CI/CD.

## Padrão de Commits (Conventional Commits)

Utilizamos a convenção do [Conventional Commits](https://www.conventionalcommits.org/):

- `feat: adiciona filtro por hierarquia de acesso`
- `fix: corrige erro de autenticação via SSO`
- `docs: atualiza guia de instalação no README`
- `style: formatação de código de acordo com o linter`

## Processo de Pull Request (PR)

1. Garanta que seu código está rodando sem erros e passando nos testes locais.
2. Abra um Pull Request apontando para a branch `develop`.
3. Preencha o template de PR detalhando o que foi feito.
4. Solicite o Code Review do **Tech Lead**.
5. Após a aprovação e o sucesso nos testes de CI, o merge poderá ser realizado.