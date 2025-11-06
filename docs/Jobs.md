#  Jobs — Pipeline Completo (Landing → Bronze → Silver → Gold)

##  Visão Geral

Este documento descreve, passo a passo, como criar e configurar os *Jobs* no **Databricks** para o pipeline do projeto **Databricks_Filmes**.  
O objetivo é **automatizar a execução dos notebooks**, desde a conexão com o Supabase até a camada **Gold**, garantindo um fluxo de dados contínuo e confiável.

---

##  Notebooks do Pipeline (Ordem de Execução)

| Ordem | Notebook | Descrição | Camada |
|:------|:----------|:-----------|:--------|
| 1️⃣ | `Conexao.ipynb` | Define as credenciais e parâmetros de conexão (módulo reutilizável) | — |
| 2️⃣ | `Filmes 001.ipynb` | Criação e ingestão da tabela inicial | *Landing* |
| 3️⃣ | `Filmes 002.ipynb` | Limpeza e padronização dos dados | *Bronze* |
| 4️⃣ | `Filmes 003.ipynb` | Transformações e enriquecimentos intermediários | *Silver* |
| 5️⃣ | `Filmes 004.ipynb` | Agregações e métricas finais | *Gold* |

---

##  1. Criando o Job no Databricks (Interface Gráfica - UI)

1. No **workspace Databricks**, acesse o menu lateral e clique em:

2. Defina um **nome para o Job**:


3. Adicione as **tarefas (tasks)** conforme a sequência abaixo:

---

###  Task 1 — Conexão (Setup)
- **Type:** Notebook  
- **Path:** `/Workspace/.../Conexao`  
- **Cluster:**  
- Recomendado: `New Job Cluster`  
- Alternativa: `Existing All-Purpose Cluster`  
- **Retry Settings:** 1 retry (opcional)  
- **Timeout:** 1 hora (ajuste conforme necessidade)

---

###  Task 2 — Landing
- **Type:** Notebook  
- **Path:** `/Workspace/.../Filmes 001`  
- **Depends on:** *Task 1 (Conexao)*

---

###  Task 3 — Bronze
- **Type:** Notebook  
- **Path:** `/Workspace/.../Filmes 002`  
- **Depends on:** *Task 2 (Landing)*

---

###  Task 4 — Silver
- **Type:** Notebook  
- **Path:** `/Workspace/.../Filmes 003`  
- **Depends on:** *Task 3 (Bronze)*

---

###  Task 5 — Gold
- **Type:** Notebook  
- **Path:** `/Workspace/.../Filmes 004`  
- **Depends on:** *Task 4 (Silver)*

---

##  2. Salvando e Executando o Job

1. Após configurar todas as tarefas, clique em **Create** (ou **Save**).  
2. Clique em **Run Now** para testar a execução.  
3. Monitore o pipeline pelo painel **Run details**, onde é possível visualizar:
- Status de cada etapa (✔️ sucesso / ❌ falha)  
- Logs de execução  
- Duração total do pipeline  

---

##  3. Automação (Agendamento)

Para automatizar o pipeline:
1. Dentro do Job criado, vá até **Schedule → Add schedule**.  
2. Configure:
- **Frequency:** diária, semanal ou personalizada  
- **Time zone:** `America/Sao_Paulo`  
- **Start time:** conforme necessidade  

---

##  4. Resultado Final

Após configurado, o Job executará automaticamente as etapas:
> Supabase → Databricks (Landing → Bronze → Silver → Gold)

Gerando um fluxo de ingestão e transformação **totalmente automatizado e auditável** dentro do **Databricks Workflows**.

---


