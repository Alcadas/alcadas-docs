# ADR-004: Persistência com Cloud Firestore e Autenticação com Firebase Auth

## Status
**Aceito** (Checkpoint 1)

## Data
2026-10-18

---

## Contexto

A solução **Alçadas** opera sob restrições técnicas e financeiras específicas:
1. **Controle Orçamentário e Custo em Nuvem:** O teto financeiro definido para todo o ciclo de desenvolvimento do projeto na Google Cloud Platform (GCP) é de **US$ 50,00**, com meta de consumo operacional entre **US$ 4,00 e US$ 10,00**, e limite de **US$ 5,00** até a Semana 4 (`RNF06`);
2. **Execução Autônoma em Desenvolvimento:** É obrigatório que o ambiente local de desenvolvimento execute 100% de forma autônoma sem gerar cobranças na nuvem, inicializável via `docker compose up` (`RNF10`);
3. **Sincronização em Tempo Real:** Atualizações de status de solicitações, novas pendências e alertas de risco devem propagar-se aos clientes ativos em menos de **1,5 segundos** (`RNF01`);
4. **Isolamento Lógico Multi-tenant:** Dados de diferentes empresas/clínicas clientes devem manter separação lógica estrita, retornando `HTTP 403` ou `404` em acessos cruzados (`RNF07`);
5. **Autenticação Segura:** Emissão de tokens JWT assinados com suporte a *Custom Claims* para papéis de usuário (RBAC) e identificador de organização (`tenantId`).

---

## Decisão

Adotamos a combinação de **Google Cloud Firestore** (modo Nativo) como banco de dados NoSQL transacional e **Firebase Auth** como provedor de identidade e autenticação gerenciada.

A implementação baseia-se em:
1. **Ambiente Local 100% Baseado em Emuladores:** No desenvolvimento e CI local, utilizamos o `firebase-tools-docker` via `docker-compose.yml`, subindo instâncias locais do Firestore Emulator (porta 8080) e Firebase Auth Emulator (porta 9099). O consumo em nuvem na fase de desenvolvimento é de exatamente **US$ 0,00**;
2. **Escrita Transacional Atômica:** Mudanças de estado e gravação de novos elos da trilha de auditoria são realizadas por meio de transações atômicas (`firestore.runTransaction()`), garantindo isolamento ACID a nível de documento e consistência de concorrência;
3. **Streams em Tempo Real:** O Firestore fornece nativamente o protocolo de escuta via WebSockets (`onSnapshot()`), permitindo alimentar os painéis reativos do frontend Angular em menos de 1,5 segundos;
4. **Isolamento de Tenants:** Toda entidade (`solicitacoes`, `alcadas`, `auditoria_elos`) armazena obrigatoriamente a propriedade `tenantId`. O NestJS filtra todas as consultas por `tenantId` e as *Firestore Security Rules* barram em nível de base qualquer leitura ou gravação de tokens cujo `tenantId` divirja do documento;
5. **Imutabilidade de Auditoria nas Security Rules:** A coleção `trilha_auditoria` possui regra restritiva de segurança:
   ```javascript
   match /tenants/{tenantId}/solicitacoes/{solicitacaoId}/trilha/{eloId} {
     allow read: if request.auth != null && request.auth.token.tenantId == tenantId;
     allow create: if request.auth != null && request.auth.token.role in ['ADMIN', 'APROVADOR', 'SOLICITANTE'];
     allow update, delete: if false; // Imutabilidade absoluta: proibida edição e exclusão
   }
   ```

### Alternativas Avaliadas e Descartadas

1. **PostgreSQL Gerenciado (Google Cloud SQL):** Avaliado pela maturidade relacional, mas descartado por custo financeiro impeditivo: uma instância mínima de Cloud SQL custa aproximadamente US$ 25 a US$ 35/mês em regime contínuo, violando imediatamente o teto orçamentário de US$ 50,00 do projeto.
2. **MongoDB Atlas:** Descartado por demandar camada de rede externa adicional, não oferecer emulação local unificada com provedor de autenticação e apresentar suporte inferior a sincronização em tempo real de baixa latência em comparação com o Firestore.
3. **Auth0 / Okta:** Descartados devido à complexidade de licenciamento e falta de integração direta com emuladores de dados locais no Docker Compose.

---

## Consequências

### Positivas
- **Orçamento Rigorosamente Preservado:** Desenvolvimento diário sem custos na nuvem e ambiente de homologação/produção serverless faturado exclusivamente por operações reais;
- **Sincronização em Tempo Real Nativa:** Elimina a necessidade de construir e manter infraestrutura complexa de WebSockets dedicada (ex.: Socket.io em cluster);
- **Segurança Reforçada em Duas Camadas:** Acesso validado primeiramente pelos Guards do NestJS e duplamente assegurado pelas Firestore Security Rules.

### Negativas / Mitigações
- **Modelagem NoSQL Não-Relacional:** Consultas complexas que exigiriam múltiplos `JOINs` no modelo relacional demandam desnormalização controlada de dados.  
  *Mitigação:* Armazenamento dos dados essenciais do solicitante e do centro de custo diretamente no documento da solicitação, mantendo a consistência via transações atômicas.
