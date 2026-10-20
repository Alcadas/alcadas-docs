# ADR-003: Backend em NestJS com Arquitetura Modular e Guards

## Status
**Aceito** (Checkpoint 1)

## Data
2026-10-18

---

## Contexto

O núcleo de negócio do **Alçadas** é responsável por operações críticas e sensíveis que exigem elevado rigor de governança:
- Validação estrita de limites financeiros por cargo/nível hierárquico (`RF01`, `RN01`);
- Execução segura da máquina de estados com transações atômicas e controle de decisões concorrentes (`RF03`, `RF10`, `RF11`);
- Execução do motor de detecção de riscos financeiros: cálculo de janelas móveis de 30 dias para anti-fracionamento (`RF05`, `RN05`) e detecção de duplicidades em intervalo inferior a 7 dias (`RF06`, `RN06`);
- Aplicação incondicional de Segregação de Funções (*SoD*), impedindo autoaprovação mesmo em cenários com delegações temporárias ativas (`RF04`, `RF12`, `RN03`, `RN12`);
- Geração determinística de selos criptográficos encadeados em SHA-256 para a trilha de auditoria (`RF07`, `RF08`, `RNF02`).

Esses requisitos demandam um framework de backend altamente modular, testável, que suporte injeção de dependências corporativa e interceptação declarativa de requisições.

---

## Decisão

Adotamos o framework **NestJS** (v11+) com **TypeScript** para o desenvolvimento da API backend (`apps/api`).

A arquitetura do backend estrutura-se em:
1. **Camadas Claras de Responsabilidade:**
   - **Controllers:** Endpoints REST documentados e tipados, responsáveis por mapear rotas HTTP, códigos de status e tratamento de exceções;
   - **Services (Domínio):** Regras de negócio puras (cálculo de alçada, cálculo anti-fracionamento, verificação de hash SHA-256);
   - **Guards:** Interceptadores de segurança que validam o token JWT (`JwtAuthGuard`), o papel hierárquico do usuário (`RolesGuard`) e a segregação de funções (`SodGuard`);
   - **Pipes:** Validação automática e sanitização de payloads de entrada via `ValidationPipe` com `class-validator` e `class-transformer`;
   - **Filters:** Tratamento global de exceções HTTP padronizando mensagens de erro JSON (`400`, `401`, `403`, `404`, `409 Conflict`, `500`).
2. **Módulos Independentes:**
   - `AuthModule`: Validação de identidade e claims do Firebase Auth;
   - `SolicitacoesModule`: Criação, consulta, listagem e máquina de estados das solicitações;
   - `AlcadasModule`: Parametrização de centros de custo e limites financeiros;
   - `RiscosModule`: Algoritmos de anti-fracionamento e duplicidade;
   - `AuditoriaModule`: Encadeamento criptográfico e conferência de integridade da trilha.

### Alternativas Avaliadas e Descartadas

1. **Express / Fastify Puro:** Descartado devido à ausência de estrutura arquitetural padronizada, demandando construção manual de injeção de dependências, middlewares e validações que aumentariam a probabilidade de falhas de segurança.
2. **Spring Boot (Java / Kotlin):** Avaliado pela maturidade corporativa, mas descartado por quebrar a homogeneidade do monorepo Nx em TypeScript, impedindo o compartilhamento direto de tipos e elevando a carga cognitiva da equipe.
3. **Go (Gin / Echo):** Descartado pelo mesmo motivo de heterogeneidade tecnológica e ausência de compartilhamento de DTOs com o frontend Angular.

---

## Consequências

### Positivas
- **Alta Coesão e Baixo Acoplamento:** Arquitetura orientada a serviços facilita a escrita de testes unitários isolados com mocks do Jest (`npm run test:api`);
- **Segurança Declarativa:** Permissões de alçada e SoD são aplicadas nos endpoints com decorators e Guards (`@UseGuards(JwtAuthGuard, RolesGuard, SodGuard)`), evitando duplicação de checagens manuais;
- **Compatibilidade Nativa com OpenAPI:** Geração e alinhamento facilitado com a especificação Swagger/OpenAPI v0.1 (`@nestjs/swagger`).

### Negativas / Mitigações
- **Overhead Estrutural Inicial:** O NestJS introduz decorators, módulos e injeção de dependências que demandam rigor da equipe.  
  *Mitigação:* Estrutura inicial padronizada criada e configurada no Nx monorepo (`apps/api`) com exemplos claros de controllers e serviços.
