# Modelagem BPMN — Processo Proposto (To-Be)

Esta seção especifica a modelagem formal de processos de negócio (**BPMN 2.0**) do sistema **Alçadas**. O fluxo substitui as comunicações informais por uma esteira padronizada, orquestrada por regras de governança, segregação de funções (*SoD*), alertas preditivos de risco e auditoria criptográfica.

---

## 1. Fluxo Principal de Submissão, Análise e Decisão

O processo orquestra a interação entre o **Solicitante**, o **Motor de Riscos e Regras**, os **Aprovadores Hierárquicos** e a **Trilha de Auditoria**.

```mermaid
flowchart TD
    classDef startEnd fill:#d4edda,stroke:#28a745,stroke-width:2px;
    classDef serviceTask fill:#d1ecf1,stroke:#17a2b8,stroke-width:1.5px;
    classDef userTask fill:#e2e3e5,stroke:#383d41,stroke-width:1.5px;
    classDef gateway fill:#fff3cd,stroke:#ffc107,stroke-width:2px;
    classDef alertTask fill:#f8d7da,stroke:#dc3545,stroke-width:2px;

    subgraph Pool["Processo de Governança de Alçadas (To-Be) — Sistema Alçadas"]
        subgraph Solicitante["Raia: Solicitante"]
            Start(["Necessidade de Despesa"]):::startEnd
            T_Submeter["Submete Solicitação\n(Valor, CC, Categoria, Justificativa)"]:::userTask
            T_Ajustar["Corrige Campos / Anexos\n(Status: EM_AJUSTE)"]:::userTask
        end

        subgraph Sistema["Raia: Sistema Alçadas (Backend & Motor de Riscos)"]
            T_ValidaSoD{"Solicitante == Aprovador?"}:::gateway
            T_RiscoFrac["Calcula Janela Móvel (30 dias)\nVerifica teto do Centro de Custo"]:::serviceTask
            G_Frac{"Fracionamento detectado?"}:::gateway
            T_RiscoDup["Busca Duplicidade (7 dias)\n(Mesmo valor, CC e fornecedor)"]:::serviceTask
            T_EloGenesis["Registra Elo na Cadeia SHA-256\n(Status: PENDENTE)"]:::serviceTask
            T_EloDecisao["Registra Elo Decisório SHA-256\n(Status Final / Ajuste)"]:::serviceTask
        end

        subgraph AprovadorN1["Raia: Aprovador Nível 1 / Nível 2"]
            G_Alcada{"Valor <= Teto da Alçada?"}:::gateway
            T_DecisaoN1["Analisa Solicitação no Painel\n(Alerta Duplicidade visível se houver)"]:::userTask
            G_DecisaoN1{"Decisão do Aprovador"}:::gateway
        end

        subgraph AprovadorN3["Raia: Diretoria / CFO (Nível 3)"]
            T_DecisaoN3["Analisa Solicitação de Alto Valor\nou Escalonada por Risco"]:::userTask
            G_DecisaoN3{"Decisão da Diretoria"}:::gateway
        end

        subgraph Auditoria["Raia: Auditoria / Trilha Imutável"]
            EndAprovada(["Solicitação APROVADA\n(Pronta para Pagamento)"]):::startEnd
            EndRejeitada(["Solicitação REJEITADA\n(Processo Encerrado)"]):::startEnd
        end
    end

    Start --> T_Submeter
    T_Submeter --> T_RiscoDup
    T_RiscoDup --> T_RiscoFrac
    T_RiscoFrac --> G_Frac
    G_Frac -- "Sim (Soma > Teto N1/N2)" --> T_EloGenesis
    G_Frac -- "Não" --> T_EloGenesis
    T_EloGenesis --> G_Alcada

    G_Alcada -- "Sim (Dentro do Teto)" --> T_ValidaSoD
    T_ValidaSoD -- "Sim (Violação SoD)" --> T_DecisaoN3
    T_ValidaSoD -- "Não (Aprovador distinto)" --> T_DecisaoN1

    G_Alcada -- "Não (Excede Teto ou Escalonada)" --> T_DecisaoN3

    T_DecisaoN1 --> G_DecisaoN1
    G_DecisaoN1 -- "Aprovar" --> T_EloDecisao
    G_DecisaoN1 -- "Rejeitar (>= 10 chars)" --> T_EloDecisao
    G_DecisaoN1 -- "Solicitar Ajuste (>= 10 chars)" --> T_EloDecisao

    T_DecisaoN3 --> G_DecisaoN3
    G_DecisaoN3 -- "Aprovar" --> T_EloDecisao
    G_DecisaoN3 -- "Rejeitar" --> T_EloDecisao
    G_DecisaoN3 -- "Solicitar Ajuste" --> T_EloDecisao

    T_EloDecisao --> G_DecisaoFinal{"Novo Estado"}:::gateway
    G_DecisaoFinal -- "APROVADA" --> EndAprovada
    G_DecisaoFinal -- "REJEITADA" --> EndRejeitada
    G_DecisaoFinal -- "EM_AJUSTE" --> T_Ajustar
    T_Ajustar --> T_Submeter
```

---

## 2. Subprocesso BPMN: Delegação Temporária e Expiração por Timer

Para atender aos requisitos `RF04` e `RN04`, o sistema implementa um subprocesso com evento de fronteira de temporização (*Boundary Timer Event*):

```mermaid
flowchart LR
    classDef startEnd fill:#d4edda,stroke:#28a745,stroke-width:2px;
    classDef task fill:#e2e3e5,stroke:#383d41,stroke-width:1.5px;
    classDef serviceTask fill:#d1ecf1,stroke:#17a2b8,stroke-width:1.5px;
    classDef timer fill:#fff3cd,stroke:#ffc107,stroke-width:2px;

    StartDel(["Aprovador Titular Ausente"]):::startEnd
    T_ConfigDel["Parametriza Substituto,\nInício e Fim da Vigência"]:::task
    T_GravaDel["Grava Delegação + Elo SHA-256"]:::serviceTask
    T_Ativa["Delegação Vigente:\nSubstituto atua na alçada delegada\n(Blindado por SoD: RN12)"]:::task
    Timer["Timer: Data/Hora Fim Atingida"]:::timer
    T_Expira["Sistema revoga acesso substituto automaticamente\n+ Grava Elo SHA-256 de Expiração"]:::serviceTask
    EndDel(["Delegação Finalizada"]):::startEnd

    StartDel --> T_ConfigDel --> T_GravaDel --> T_Ativa
    T_Ativa --> Timer --> T_Expira --> EndDel
```

---

## 3. Subprocesso BPMN: Verificação de Integridade Criptográfica

Para atender aos requisitos `RF08` e `RN08`, a controladoria e auditores contam com o fluxo de checagem em tempo de execução:

```mermaid
flowchart TD
    classDef startEnd fill:#d4edda,stroke:#28a745,stroke-width:2px;
    classDef task fill:#e2e3e5,stroke:#383d41,stroke-width:1.5px;
    classDef gateway fill:#fff3cd,stroke:#ffc107,stroke-width:2px;
    classDef danger fill:#f8d7da,stroke:#dc3545,stroke-width:2px;
    classDef success fill:#d4edda,stroke:#28a745,stroke-width:2px;

    StartAud(["Auditor seleciona solicitação"]):::startEnd
    T_Carrega["Carrega todos os elos ordenados (seq = 0..n)"]:::task
    T_Itera["Recalcula Hash SHA-256 de cada elo recursivamente\nH_esperado = SHA-256(H_ant + timestamp + author + payload)"]:::task
    G_Match{"H_calculado == H_armazenado em 100% dos elos?"}:::gateway
    R_Ok["Exibe Selo Verde: 'Cadeia Íntegra e Verificada' (< 1s)"]:::success
    R_Fail["Exibe Alerta Vermelho: 'Inconsistente / Adulterado' (< 1s)"]:::danger

    StartAud --> T_Carrega --> T_Itera --> G_Match
    G_Match -- "Sim" --> R_Ok
    G_Match -- "Não" --> R_Fail
```

---

## 4. Comparativo de Ganhos: As-Is vs. To-Be

| Dimensão | Processo As-Is (Startop LTDA) | Processo To-Be (Sistema Alçadas) |
| :--- | :--- | :--- |
| **Canal de Entrada** | Mensagens dispersas (WhatsApp/E-mail) | Formulário web tipado com validação de campos obrigatórios (`RF02`) |
| **Controle de Alçadas** | Informal e dependente da memória dos gestores | Parametrizado por Centro de Custo com limites matemáticos rígidos (`RF01`, `RN01`) |
| **Mitigação de Riscos** | Zero detecção de fracionamento ou duplicidade | Motor de riscos atuando em janelas móveis de 30 dias e 7 dias (`RF05`, `RF06`) |
| **Segregação de Funções** | Frequente autoaprovação de despesas | Bloqueio automático de autoaprovação via Guard (`RN03`, `RN12`) |
| **Auditoria e Evidências** | Planilhas editáveis sem controle de versão | Encadeamento criptográfico SHA-256 imutável à prova de adulteração (`RF07`, `RF08`) |
| **Tempo de Notificação** | Horas ou dias aguardando retorno | Sincronização em tempo real via streams em $< 1,5$ s (`RNF01`) |
