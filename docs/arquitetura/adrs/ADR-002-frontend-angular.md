# ADR-002: Frontend Web em Angular com Formulários Reativos

## Status
**Aceito** (Checkpoint 1)

## Data
2026-10-18

---

## Contexto

A camada de apresentação do **Alçadas** atende a perfis com necessidades operacionais distintas (solicitantes operacionais, gestores de obras/clínicas, aprovadores e auditores de conformidade). O sistema exige:
- Formulários complexos com validações síncronas e assíncronas (ex.: centros de custo, faixas de valor, exigência de justificativa com mínimo de 30 caracteres para valores acima da alçada base - `CA-02.2`);
- Tratamento explícito e padronizado para os três estados visuais fundamentais: *loading* (carregamento), *empty* (lista vazia) e *error* (erro com opção de nova tentativa) em 100% das telas (`RNF08`);
- Consumo de fluxos reativos em tempo real para atualização imediata de status e alertas de risco sem necessidade de recarregar a tela (`RNF01` - meta `< 1,5 s`);
- Total alinhamento com a arquitetura corporativa e o ecossistema TypeScript.

---

## Decisão

Adotamos o framework **Angular** (v22+) com **TypeScript** para o desenvolvimento da aplicação web cliente (`apps/web`).

A implementação do frontend fundamenta-se nos seguintes princípios técnicos:
1. **Formulários Reativos (*Reactive Forms*):** Utilização de `FormGroup`, `FormControl` e validadores customizados para blindar a entrada de dados antes mesmo da submissão HTTP;
2. **RxJS e Signals:** Gerenciamento reativo de estado de tela com Signals para reatividade local e RxJS para multiplexação de *streams* de eventos em tempo real oriundos do backend/Firestore;
3. **Componentes Padronizados de Estado Visual:** Criação de componentes compartilhados reutilizáveis para `StateLoadingComponent`, `StateEmptyComponent` e `StateErrorComponent`, cumprindo o critério inegociável do *Definition of Done* e do `RNF08`;
4. **Proxy Local de Desenvolvimento:** Configuração de `proxy.conf.json` no Nx para redirecionamento transparente de chamadas `/api` para o backend local na porta `3000`.

### Alternativas Avaliadas e Descartadas

1. **React com Next.js:** Avaliado positivamente pelo ecossistema amplo, mas descartado por não oferecer uma estrutura formal padronizada de injeção de dependências e por exigir bibliotecas externas fragmentadas para formulários, validação e gerenciamento de estado.
2. **Vue.js:** Descartado devido à menor maturidade na integração nativa com monorepos corporativos governados por Nx com tipagem estrita de ponta a ponta.

---

## Consequências

### Positivas
- **Estrutura Robusta e Opinativa:** A arquitetura em módulos, componentes standalone e injeção de dependências do Angular reduz divergências de estilo de código entre os membros da equipe.
- **Validação Segura no Cliente:** Regras de negócio de preenchimento (formatação monetária, limites de caracteres, campos obrigatórios) são validadas de forma reativa e consistente.
- **Integração Perfeita com o Monorepo:** O plugin `@nx/angular` oferece scripts de linting (`angular-eslint`), compilação AOT ultrarrápida e testes automatizados configurados de fábrica.

### Negativas / Mitigações
- **Tamanho do Bundle Inicial:** Frameworks baseados em Angular costumam gerar artefatos iniciais ligeiramente superiores a bibliotecas mínimas.  
  *Mitigação:* Utilização de *Lazy Loading* nas rotas da aplicação, compilação *Ahead-Of-Time (AOT)* e árvore de sacudimento (*Tree Shaking*) habilitada nas builds de produção.
