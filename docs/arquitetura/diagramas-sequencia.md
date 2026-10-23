# Diagramas de Sequência UML

Esta página detalha as interações dinâmicas e síncronas/assíncronas entre os componentes do **Alçadas**, com foco nos quatro maiores desafios de engenharia do sistema:

1. **Tratamento de Decisões Concorrentes e Transações Atômicas (`RF11` / `RN11`)**
2. **Motor de Riscos: Prevenção de Fracionamento e Duplicidade (`RF05` / `RF06`)**
3. **Cadeia Criptográfica SHA-256 e Verificação de Integridade (`RF07` / `RF08`)**
4. **Segregação de Funções (*SoD*) em Delegações Temporárias (`RF04` / `RF12`)**

---

## 1. Concorrência e Transações Atômicas (HTTP 409 Conflict)

Quando dois aprovadores com competência sobre o mesmo pedido (ou o titular e seu delegado) submetem decisões simultâneas, o sistema utiliza a transação nativa do Firestore (`runTransaction`) para garantir controle otimista de concorrência:

```mermaid
sequenceDiagram
    autonumber
    actor A1 as Diretor Titular
    actor A2 as Diretor Substituto (Delegado)
    participant UI as Angular Frontend
    participant API as NestJS Controller / Guard
    participant SM as State Machine Service
    participant DB as Cloud Firestore (Transacional)

    par Submissão Concorrente
        A1->>UI: Clica em "Aprovar"
        UI->>API: POST /api/v1/requests/{id}/approve (versão 1)
    and
        A2->>UI: Clica em "Rejeitar"
        UI->>API: POST /api/v1/requests/{id}/reject (versão 1)
    end

    critical Requisição A1 (Processada Primeiro)
        API->>DB: Inicia Firestore.runTransaction()
        DB-->>API: Retorna documento atual (status: PENDENTE, version: 1)
        API->>SM: Valida transição PENDENTE -> APROVADA
        SM-->>API: Transição Válida
        API->>DB: Grava status APROVADA, version: 2 + Novo Elo SHA-256
        DB-->>API: Commit confirmado com sucesso
        API-->>UI: 200 OK { id, status: "APROVADA", version: 2 }
        UI-->>A1: Notificação: "Solicitação Aprovada com sucesso"
    end

    critical Requisição A2 (Execução Concorrente Conflitante)
        API->>DB: Inicia Firestore.runTransaction()
        DB-->>API: Retorna documento atualizado (status: APROVADA, version: 2)
        API->>SM: Tenta validar transição APROVADA -> REJEITADA
        SM-->>API: Erro: Transição Ilegal (Estado Terminal)
        API->>DB: Aborta transação
        API-->>UI: 409 Conflict { error: "Solicitação já foi finalizada por outro aprovador" }
        UI-->>A2: Alerta em tela: "Conflito: esta solicitação já foi decidida"
    end

    DB-->>UI: Evento em Real-time Stream atualiza tela para APROVADA (< 1,5s)
```

---

## 2. Motor de Riscos: Anti-Fracionamento e Detecção de Duplicidade

Antes de disponibilizar a solicitação para a fila de aprovação regular, o **Motor de Riscos** processa consultas analíticas na base do *tenant*:

```mermaid
sequenceDiagram
    autonumber
    actor Solicitante as Solicitante Operacional
    participant UI as Angular Frontend
    participant API as NestJS Requests Controller
    participant Risk as RiskEngineService
    participant DB as Cloud Firestore

    Solicitante->>UI: Preenche formulário (Valor: R$ 3.000, CC: Marketing, Fornecedor: Gráfica Alfa)
    UI->>API: POST /api/v1/requests (Payload + Bearer JWT)
    
    API->>Risk: evaluateRisk(payload, tenantId)
    
    activate Risk
    Note over Risk,DB: 1. Checagem de Duplicidade (RN06)
    Risk->>DB: Query: requests onde tenantId == X, costCenter == Marketing, vendor == Gráfica Alfa, amount == 3000, createdAt >= (NOW - 7 dias)
    DB-->>Risk: Retorna 1 registro encontrado
    Risk->>Risk: Marca flag: suspiciousDuplicate = true
    
    Note over Risk,DB: 2. Checagem de Fracionamento (RN05)
    Risk->>DB: Query: requests onde tenantId == X, costCenter == Marketing, category == Material, createdAt >= (NOW - 30 dias)
    DB-->>Risk: Retorna soma total de R$ 4.200 nos últimos 30 dias
    Risk->>Risk: Nova soma total = R$ 4.200 + R$ 3.000 = R$ 7.200
    Risk->>Risk: Teto da alçada N1 é R$ 5.000 -> Excedeu teto (R$ 7.200 > R$ 5.000)
    Risk->>Risk: Marca flag: splitSuspected = true (Escalonamento automático para Diretoria)
    deactivate Risk

    API->>DB: Salva Request com flags { suspiciousDuplicate: true, splitSuspected: true, requiredTier: "N3_DIRETORIA" }
    DB-->>API: Documento persistido
    API-->>UI: 201 Created (Solicitação criada com avisos de risco)
    UI-->>Solicitante: Exibe confirmação com aviso de escalonamento para Nível 3
```

---

## 3. Cadeia Criptográfica SHA-256 e Verificação de Integridade

Fluxo de criação do selo criptográfico encadeado e o posterior recálculo de integridade pelo auditor:

```mermaid
sequenceDiagram
    autonumber
    actor User as Usuário / Aprovador
    actor Auditor as Auditor / Compliance
    participant API as NestJS Audit Module
    participant Crypto as CryptoHasher (Node.js crypto)
    participant DB as Cloud Firestore

    User->>API: Transição de Estado (ex.: PENDENTE -> APROVADA)
    API->>DB: Busca último bloco da trilha (Elo n-1)
    DB-->>API: Retorna Elo n-1 (Hash: "a3b8c9...")
    
    API->>Crypto: Gera Payload Canônico (previousHash, timestampUTC, authorId, action, metadata)
    Crypto->>Crypto: Executa SHA-256(canonicalPayload)
    Crypto-->>API: Retorna Hash Atual H_n ("f5e4d2...")
    
    API->>DB: Grava novo bloco imutável na subcoleção /requests/{id}/audit_blocks/{seq}
    DB-->>API: Sucesso

    opt Processo de Verificação de Integridade (RF08 / RN08)
        Auditor->>API: GET /api/v1/requests/{id}/audit-trail/verify
        API->>DB: Carrega 100% dos blocos (ordenados por seq ASC)
        DB-->>API: Retorna lista de blocos [B0, B1, B2, ..., Bn]
        
        loop Para cada bloco de 1 até n
            API->>Crypto: Recalcula SHA-256(B_{i-1}.hash + B_i.timestamp + B_i.author + B_i.payload)
            alt Hash recalculado != B_i.currentHash ou B_i.previousHash != B_{i-1}.currentHash
                API-->>Auditor: 200 OK { isTampered: true, compromisedBlockIndex: i, status: "Inconsistente / Adulterado" }
            end
        end
        API-->>Auditor: 200 OK { isTampered: false, totalBlocks: n, status: "Cadeia Íntegra e Verificada" }
    end
```

---

## 4. Segregação de Funções (*SoD*) em Delegações Temporárias

Fluxo onde um usuário que recebeu delegação de um Diretor tenta aprovar um pedido de sua própria autoria:

```mermaid
sequenceDiagram
    autonumber
    actor Substituto as Solicitante com Delegação Ativa
    participant UI as Angular Frontend
    participant Guard as NestJS SoDGuard & RBACGuard
    participant API as RequestsController
    participant Audit as AuditService
    participant DB as Cloud Firestore

    Note over Substituto: Usuário possui role de Solicitante e uma delegação ativa de Diretor
    Substituto->>UI: Acessa solicitação criada por ele mesmo e tenta clicar em "Aprovar"
    UI->>API: POST /api/v1/requests/{id}/approve (Bearer JWT)
    
    API->>Guard: canActivate(context)
    activate Guard
    Guard->>DB: Busca Request(id)
    DB-->>Guard: Retorna Request (authorId: "usr_123")
    Guard->>Guard: Extrai userId do JWT ("usr_123")
    
    Guard->>Guard: Validação SoD: authorId ("usr_123") == requesterId ("usr_123")
    Note over Guard: Conflito detectado: Autoaprovação estritamente proibida (RN03 / RN12)
    
    Guard->>Audit: Registra tentativa de violação de SoD na trilha
    Audit->>DB: Grava log de segurança { event: "SOD_VIOLATION_BLOCKED", userId: "usr_123", requestId: id }
    
    Guard-->>API: Retorna false (Bloqueio)
    deactivate Guard
    
    API-->>UI: 403 Forbidden { code: "SOD_SELF_APPROVAL_PROHIBITED", message: "Conflito de Funções: O autor da despesa não pode aprová-la, mesmo sob delegação." }
    UI-->>Substituto: Mensagem de erro em destaque na tela
```
