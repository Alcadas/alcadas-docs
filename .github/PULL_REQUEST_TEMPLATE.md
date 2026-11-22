## 📌 Tipo de Mudança
- [ ] Nova funcionalidade (`feat`)
- [ ] Correção de bug (`fix`)
- [ ] Documentação (`docs`)
- [ ] Refatoração ou ajuste técnico (`refactor` / `chore`)
- [ ] Testes automatizados (`test`)
- [ ] Ajuste de CI/CD (`ci`)

## 📝 Descrição das Alterações
Descreva de forma concisa e clara o que foi implementado, alterado ou corrigido.

## 🔗 Issues e Requisitos Relacionados
- Closes # (insira o número da issue)
- Rastreabilidade: (ex.: `RF03`, `RN05`, `US-02`, `RNF07`)

## 🧪 Como Testar?
1. Execute o comando: `...`
2. Valide o endpoint, tela ou funcionalidade: `...`
3. Verifique o comportamento esperado: `...`

## 📸 Capturas de Tela / Logs (Se aplicável)
*Adicione capturas de tela da interface web, evidências de testes ou logs da API, se houver.*

---

## ✅ Definition of Done
> Conforme a [Definition of Done](https://github.com/Alcadas/alcadas-docs/blob/main/docs/governanca/dod.md) (Caderno B2B v2, seção 1.6). Item que não se aplica fica com `N/A`.

**Autor**
- [ ] Branch no padrão `docs/#<issue>-descricao` e commits no padrão Conventional Commits.
- [ ] `mkdocs build --strict` passou localmente e o CI está verde.
- [ ] Mudança em rota da API está igual no `docs/api/openapi.yaml`, com versão e histórico atualizados.
- [ ] Decisão técnica nova ou que contraria uma anterior tem ADR em `docs/arquitetura/adrs/`.
- [ ] Rastreabilidade atualizada quando muda requisito, critério de aceite, rota ou teste.
- [ ] Nenhum dado pessoal (nome, CPF, telefone de entrevistado) nem segredo no texto ou nas imagens.

**Revisor** (pessoa diferente do autor)
- [ ] Li o conteúdo renderizado (não só o diff) e os links funcionam.
