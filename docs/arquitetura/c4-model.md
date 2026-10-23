# Arquitetura C4 Model

O modelo **C4** (Contexto, Contêineres, Componentes e Código) fornece uma abordagem hierárquica e padronizada para visualizar a arquitetura de software do sistema **Alçadas**.

---

## 1. Nível 1: Diagrama de Contexto de Sistema (System Context)

Apresenta o sistema Alçadas no centro do ecossistema corporativo, seus diferentes perfis de usuários e as integrações externas:

```mermaid
flowchart TB
    classDef person fill:#08427b,stroke:#052e56,color:#fff;
    classDef system fill:#1168bd,stroke:#0b4884,color:#fff;
    classDef extSystem fill:#999999,stroke:#666666,color:#fff;

    Solicitante["Usuário Solicitante\n[Pessoa]\nColaborador operacional que submete pedidos de compras e reembolsos"]:::person
    Aprovador["Aprovadores (N1/N2/Diretoria)\n[Pessoa]\nGestores que avaliam solicitações financeiras dentro de suas alçadas"]:::person
    Auditor["Auditor / Compliance\n[Pessoa]\nProfissional que verifica a integridade dos elos da trilha"]:::person

    Alcadas["Sistema Alçadas\n[Sistema de Software]\nPlataforma de workflow de alçadas, motor de riscos e trilha criptográfica"]:::system

    FirebaseAuth["Firebase Authentication\n[Sistema Externo]\nAutenticação segura de usuários e emissão de tokens JWT"]:::extSystem
    GCPFirestore["Google Cloud Firestore\n[Sistema Externo]\nBanco NoSQL em nuvem com sincronização em tempo real e transações"]:::extSystem
    ERPExterno["ERP Contábil / Financeiro\n[Sistema Externo Futuro]\nSistema corporativo de liquidação bancária"]:::extSystem

    Solicitante -->|"Cria pedidos, reenvia ajustes"| Alcadas
    Aprovador -->|"Aprova, rejeita, solicita ajuste, delega alçada"| Alcadas
    Auditor -->|"Valida cadeia SHA-256 e exporta relatórios"| Alcadas

    Alcadas -->|"Valida credenciais e papéis"| FirebaseAuth
    Alcadas -->|"Lê e grava transações atômicas e streams"| GCPFirestore
    Alcadas -.->|"Exporta solicitações aprovadas (CSV/JSON)"| ERPExterno
```

---

## 2. Nível 2: Diagrama de Contêineres (Containers)

Evidencia a composição em nível de contêineres e aplicações executáveis:

```mermaid
flowchart TB
    classDef container fill:#438dd5,stroke:#2e6295,color:#fff;
    classDef db fill:#2b7bb9,stroke:#1d5580,color:#fff;
    classDef user fill:#08427b,stroke:#052e56,color:#fff;

    Usuario["Usuário Corporativo\n[Navegador Web / Mobile]"]:::user

    subgraph FronteiraAlcadas["Fronteira do Sistema Alçadas (Monorepo Nx)"]
        SPA["Frontend Angular\n[Contêiner: Single-Page Application]\nInterface reativa, formulários com Reactive Forms e visualização em tempo real"]:::container
        API["Backend NestJS\n[Contêiner: Node.js / TypeScript API]\nCentraliza regras de negócio, motor de riscos, controle SoD e máquina de estados"]:::container
    end

    AuthServ["Firebase Auth\n[Serviço Cloud / Emulador Local]"]:::container
    FirestoreDB[("Cloud Firestore\n[Banco NoSQL / Emulador Local]\nPersistência de documentos e subcoleções de blocos de auditoria")]:::db

    Usuario -->|"Interage via HTTPS"| SPA
    SPA -->|"1. Autentica e obtém JWT"| AuthServ
    SPA -->|"2. Requisições REST com Bearer Token"| API
    SPA -.->|"3. Real-time Streams (< 1,5s)"| FirestoreDB
    API -->|"Valida tokens e claims RBAC"| AuthServ
    API -->|"Executa transações atômicas e queries analíticas"| FirestoreDB
```

---

## 3. Nível 3: Diagrama de Componentes (Backend NestJS)

Detalha os módulos, controladores, serviços e guards do **Backend NestJS**:

```mermaid
flowchart TD
    classDef controller fill:#85bbf0,stroke:#5d82a8,color:#000;
    classDef service fill:#d4e6f1,stroke:#a9cce3,color:#000;
    classDef guard fill:#f9e79f,stroke:#d4ac0d,color:#000;
    classDef db fill:#2b7bb9,stroke:#1d5580,color:#fff;

    subgraph NestJS["API NestJS (TypeScript)"]
        ReqCtrl["RequestsController\n[REST Controller]"]:::controller
        DelCtrl["DelegationsController\n[REST Controller]"]:::controller
        AuditCtrl["AuditController\n[REST Controller]"]:::controller

        AuthGuard["FirebaseAuthGuard\n[Guard]"]:::guard
        RBACGuard["RBACGuard\n[Guard]"]:::guard
        SoDGuard["SoDGuard\n[Guard - RN03/RN12]"]:::guard

        SMService["StateMachineService\n[Service]"]:::service
        RiskService["RiskEngineService\n[Service - RN05/RN06]"]:::service
        CryptoService["CryptoAuditService\n[Service - SHA-256]"]:::service
        TenantService["TenantContextService\n[Service - RNF07]"]:::service
    end

    Firestore[("Cloud Firestore")]:::db

    ReqCtrl --> AuthGuard
    AuthGuard --> RBACGuard
    RBACGuard --> SoDGuard
    SoDGuard --> SMService
    ReqCtrl --> RiskService
    SMService --> CryptoService
    SMService --> TenantService

    RiskService -->|"Queries de janela 7d e 30d"| Firestore
    SMService -->|"Transação atômica runTransaction()"| Firestore
    CryptoService -->|"Persiste AuditBlock imutável"| Firestore
```

---

## 4. Nível 3: Diagrama de Componentes (Frontend Angular)

Detalha a arquitetura reativa do **Frontend Angular**:

```mermaid
flowchart TD
    classDef comp fill:#85bbf0,stroke:#5d82a8,color:#000;
    classDef service fill:#d4e6f1,stroke:#a9cce3,color:#000;
    classDef state fill:#d5f5e3,stroke:#82e0aa,color:#000;

    subgraph AngularApp["Single-Page Application Angular (TypeScript)"]
        subgraph Paginas["Componentes de Visão / Telas"]
            SubmitPage["RequestSubmissionComponent\n(Reactive Forms & Anexos)"]:::comp
            PendingListPage["PendingApprovalsComponent\n(Painel Decisório em Tempo Real)"]:::comp
            AuditPage["AuditTrailViewerComponent\n(Verificador de Selos SHA-256)"]:::comp
            DelegationPage["DelegationManagerComponent\n(Gestão de Vigências)"]:::comp
        end

        subgraph Core["Serviços e Estado"]
            AuthService["AuthService\n(Firebase SDK & JWT)"]:::service
            RequestService["RequestDataService\n(HTTP Client REST)"]:::service
            StreamService["FirestoreRealtimeService\n(RxJS Observables)"]:::service
            RiskFeedbackService["RiskFeedbackService\n(Banners de Alerta Preventivo)"]:::service
        end
    end

    SubmitPage --> RequestService
    SubmitPage --> RiskFeedbackService
    PendingListPage --> StreamService
    PendingListPage --> RequestService
    AuditPage --> RequestService
    DelegationPage --> RequestService
    RequestService --> AuthService
```
