# ADR-001: Adoção de Monorepo com Nx e TypeScript Ponta a Ponta

## Status
**Aceito** (Checkpoint 1)

## Data
2026-10-18

---

## Contexto

O projeto **Alçadas** exige o desenvolvimento simultâneo de uma aplicação web (*frontend*), uma API de regras de negócio (*backend*), testes ponta a ponta e uma biblioteca compartilhada de modelos de dados (*schemas*, DTOs e entidades). O ciclo de vida do projeto é restrito a **9 semanas** de desenvolvimento intensivo em equipe, necessitando de alta velocidade de iteração, consistência nos contratos de dados e integração contínua (CI) eficiente.

A fragmentação do código em múltiplos repositórios isolados (*polyrepo*) acarreta riscos conhecidos:
- Descompasso de versões entre interfaces consumidas pelo frontend e geradas pelo backend;
- Necessidade de publicar pacotes privados no NPM ou sincronizar submódulos Git;
- Dificuldade em executar e validar mudanças atômicas em PRs únicos que envolvem frontend e backend;
- Configuração duplicada de linters, ferramentas de formatação e scripts de build.

---

## Decisão

Adotamos a arquitetura de **Monorepo gerenciado pelo Nx** (`alcadas-core`) com **TypeScript estrito ponta a ponta** tanto no cliente quanto no servidor.

A estrutura do repositório organiza-se em:
- `apps/web`: Aplicação frontend desenvolvida em Angular;
- `apps/api`: Aplicação backend desenvolvida em NestJS;
- `apps/api-e2e`: Testes de integração e ponta a ponta da API com Jest e axios;
- `apps/web-e2e`: Testes end-to-end visuais e de interface com Playwright;
- `libs/shared/models`: Biblioteca compartilhada contendo tipos primitivos, enums de status (`PENDENTE`, `EM_AJUSTE`, `APROVADA`, `REJEITADA`), interfaces de solicitações e contratos DTO.

### Alternativas Avaliadas e Descartadas

1. **Repositórios Independentes (Polyrepo):** Descartado devido ao atrito de sincronização de contratos e lentidão no CI em um projeto de apenas 9 semanas.
2. **Turborepo:** Avaliado positivamente pela velocidade de build, mas descartado por oferecer suporte menos robusto e geradores automáticos inferiores para os ecossistemas combinados de Angular e NestJS em comparação com o Nx.
3. **Lerna Puro:** Descartado por ser focado prioritariamente em versionamento/publicação de pacotes em registros externos, não em orquestração de tarefas e cache de grafo de dependências.

---

## Consequências

### Positivas
- **Contratos Únicos e Fortemente Tipados:** Mudanças em DTOs ou enums no `libs/shared/models` causam erros de compilação imediatos no frontend e backend durante o build, impedindo quebras de contrato em produção.
- **Execução Otimizada (`nx affected`):** O pipeline de CI valida, compila e testa exclusivamente os projetos afetados pelas alterações do commit, reduzindo drasticamente o tempo de validação.
- **Governança Unificada:** Padrões de código (`.editorconfig`, ESLint, Prettier) e configurações de CI/CD centralizados no repositório.

### Negativas / Mitigações
- **Curva de Aprendizado de Ferramentas Nx:** Membros da equipe precisam se habituar aos comandos `nx run`, `nx run-many` e geradores de código.  
  *Mitigação:* Criação de atalhos convencionais no `package.json` (`npm run test`, `npm run build`, `npm run lint`, `npm run start:web`, `npm run start:api`) e documentação detalhada no README e CONTRIBUTING do repositório.
