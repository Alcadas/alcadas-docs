# ADR-008: Firestore Fechado para o Navegador (Security Rules deny-all)

## Status
**Aceito** (Checkpoint 2) — **substitui parcialmente o [ADR-007](ADR-007-rbac-e-sod.md)**, seção "Camada 2: Cloud Firestore (Security Rules)".

## Data
2026-11-01

---

## Contexto

O [ADR-007](ADR-007-rbac-e-sod.md) previa Security Rules que liberavam leitura e criação de documentos para usuários autenticados do mesmo tenant (`request.auth.token.tenantId == tenantId`), inclusive na trilha de auditoria.

No Checkpoint 2 a arquitetura ficou definida assim:

- toda leitura e escrita passa pela API NestJS, que já confere token, tenant, papel, alçada e SoD;
- a API acessa o Firestore com credencial de serviço (Admin SDK / emulador), que **ignora** as Security Rules;
- o frontend Angular não usa o SDK do Firestore — só chama a API.

Liberar acesso direto ao navegador criaria um segundo caminho de escrita que não passa pela máquina de estados ([ADR-005](ADR-005-maquina-de-estados.md)) nem gera elo na trilha ([ADR-006](ADR-006-cadeia-de-hash.md)). Um usuário poderia, por exemplo, criar um elo "válido" na trilha direto pelo SDK. O Caderno B2B v2 exige *Security Rules deny-all* no Checkpoint 2.

---

## Decisão

1. O arquivo `firestore.rules` do `alcadas-core` **nega toda leitura e escrita** vindas de clientes:

   ```javascript
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if false;
       }
     }
   }
   ```

2. O `firebase.json` aponta para esse arquivo (`"firestore": { "rules": "firestore.rules" }`), para que o emulador aplique as mesmas regras de produção.
3. A defesa em profundidade do ADR-007 passa a ser: **guards da API (camada 1) + Firestore fechado (camada 2)**. As regras por tenant do ADR-007 ficam como referência caso, no futuro, o frontend precise ler dados em tempo real direto do Firestore.
4. Abrir qualquer caminho nas regras exige um novo ADR, com teste no emulador provando o isolamento de tenant e a imutabilidade da trilha.

---

## Consequências

### Positivas
- Um único caminho de escrita: tudo passa pela máquina de estados, pelo RBAC/SoD e gera elo na trilha.
- Regras simples de testar: com `@firebase/rules-unit-testing`, `assertFails` em leitura e escrita para qualquer usuário (responsabilidade de DevOps + QA, issue `alcadas-core#10`).

### Negativas / Mitigações
- **Tempo real (`RNF01`)** não pode usar `onSnapshot` direto do navegador.
  *Mitigação:* no CP2 o frontend recarrega por chamada à API; se o requisito de < 1,5 s exigir push, avaliar SSE/WebSocket pela API ou abrir leitura restrita por ADR próprio.
- Todo acesso depende da disponibilidade da API.
  *Mitigação:* `/readyz` e alertas de 5xx no Checkpoint 3.
