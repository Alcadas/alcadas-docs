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

## 🎯 Checklist de Qualidade — Definition of Done (DoD)
> Conforme estabelecido na [Definition of Done](docs/governanca/dod.md) (Caderno B2B v2, Seção 1.6):

- [ ] **1. Código Funcional e Testes:** O código foi testado localmente e os testes unitários/integração passaram sem erros?
- [ ] **2. Critérios de Aceite e Cobertura:** Todos os Critérios de Aceite (CAs) da User Story correspondente foram validados?
- [ ] **3. Build e CI Verde:** O comando `npm run build` (ou `mkdocs build --strict`) executou sem erros e o pipeline do CI está verde?
- [ ] **4. Padrões de Código e Linter:** Executou o linter e o código está em conformidade com o `.editorconfig` e sem PII/segredos em logs?
- [ ] **5. Documentação Atualizada:** A documentação correspondente (OpenAPI, ADRs, arquitetura ou guias) foi atualizada?
- [ ] **6. Revisão por Pares (Peer Review):** O PR segue o padrão semântico de branch/commit e está pronto para revisão por pelo menos um par?
