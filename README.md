# Alçadas - Documentação

## Descrição
> Sistema de Workflow de Alçadas de Aprovação e Rastreabilidade Operacional

Este repositório centraliza toda a documentação técnica, artefatos de arquitetura, requisitos de negócio e especificações do projeto **Alçadas**.

## Objetivo
> Automatizar e estruturar o fluxo de aprovação de solicitações (compras, pagamentos e reembolsos), transformando informações implícitas e regras dispersas em fatos auditáveis e dados concretos.

## Principais Destaques
- **Alçadas e Regras Configuráveis:** Validação automática de limites de valor, centros de custo e aprovações encadeadas.
- **Prevenção de Fraudes e Riscos:** Detecção preventiva de fracionamento de compras, solicitações duplicadas e conflitos de interesse.
- **Trilha de Auditoria Auditável:** Registro encadeado e imutável de todas as ações e decisões tomadas no fluxo.

---

## Como Executar a Documentação Localmente

### Pré-requisitos
- Python 3.10 ou superior
- Pip (gerenciador de pacotes Python)

### Passo a Passo

1. **Clone o repositório:**
   ```bash
   git clone https://github.com/Alcadas/docs.git
   cd docs
   ```

2. **Crie e ative o ambiente virtual:**
   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   # No Windows: .venv\Scripts\activate
   ```

3. **Instale as dependências:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Inicie o servidor de desenvolvimento do MkDocs:**
   ```bash
   mkdocs serve
   ```

5. **Acesse no navegador:**
   Abra http://127.0.0.1:8000 para visualizar a documentação com recarregamento em tempo real (*live reload*).

6. **Validar integridade do build:**
   ```bash
   mkdocs build --strict
   ```

---

## Como Contribuir

Antes de abrir um *Pull Request*, leia o nosso guia de contribuição em `CONTRIBUTING.md`.

---

## Integrantes & Mantenedores

- **Project Owner:** Matheus Ribeiro Szervinsk
- **Tech Lead:** Henrique Schneider
- **DevOps:** Pedro Henrique Gomes
- **Backend:** Luis
- **Frontend:** Ana Clara
- **QA:** Leticia

---

Alçadas - Laboratório Makers - Universidade de Brasília (UnB)
