# Catálogo de Decisões de Arquitetura (ADRs)

Este catálogo documenta os **Registros de Decisões de Arquitetura** (*Architecture Decision Records - ADRs*) do sistema **Alçadas**. As decisões seguem o padrão formal da indústria (formato de Michael Nygard: *Status, Contexto, Decisão e Consequências*), em estrita conformidade com o **Caderno B2B v2** e o **Guia do Checkpoint 1**.

---

## Índice de ADRs

| Código | Título | Status | Requisitos Relacionados | Data de Aprovação |
| :--- | :--- | :--- | :--- | :--- |
| [**ADR-001**](ADR-001-monorepo-nx.md) | Adoção de Monorepo com Nx e TypeScript Ponta a Ponta | **Aceito** | `RNF05`, Governança de Contratos | 2026-10-18 |
| [**ADR-002**](ADR-002-frontend-angular.md) | Frontend Web em Angular com Formulários Reativos | **Aceito** | `RNF01`, `RNF08`, `RF02`, `RF03` | 2026-10-18 |
| [**ADR-003**](ADR-003-backend-nestjs.md) | Backend em NestJS com Arquitetura Modular e Guards | **Aceito** | `RF01`–`RF12`, `RNF02`, `RNF03` | 2026-10-18 |
| [**ADR-004**](ADR-004-firestore-e-firebase-auth.md) | Persistência com Cloud Firestore e Firebase Auth | **Aceito** | `RNF01`, `RNF06`, `RNF07`, `RNF10` | 2026-10-18 |
| [**ADR-005**](ADR-005-maquina-de-estados.md) | Máquina de Estados Finita e Controle de Concorrência Otimista | **Aceito** | `RF03`, `RF10`, `RF11` (`US-05`) | 2026-10-18 |
| [**ADR-006**](ADR-006-cadeia-de-hash.md) | Trilha de Auditoria Criptográfica com Hashes SHA-256 | **Aceito** | `RF07`, `RF08`, `RNF02` (`US-07`, `US-08`) | 2026-10-18 |
| [**ADR-007**](ADR-007-rbac-e-sod.md) | Controle de Acesso por Papéis (RBAC) e Segregação de Funções (SoD) | **Aceito** | `RNF03`, `RF03`, `RF04`, `RF12` (`US-04`) | 2026-10-18 |

---

## Visão Geral das Decisões do Checkpoint 1

As decisões de arquitetura foram orientadas por três pilares inegociáveis do projeto:

1. **Governança Estrita e Imutabilidade:** Garantir que nenhuma aprovação ocorra sem limite verificado, que autoaprovações sejam sumariamente bloqueadas (*SoD*) e que qualquer adulteração manual na base de dados seja detectada deterministicamente em menos de 1 segundo através de elos criptográficos SHA-256 encadeados.
2. **Tempo Real e Usabilidade:** Assegurar atualização dos painéis operacionais sem necessidade de recarregar páginas (`< 1,5 s`), com tratamento visual explícito de *loading*, *empty* e *error*.
3. **Eficiência Orçamentária e Portabilidade:** Permitir execução 100% autônoma em ambiente local de desenvolvimento através do Docker Compose e dos emuladores do Firebase, limitando o consumo de nuvem (teto de US$ 50,00 na GCP) e garantindo paridade de ambientes.
