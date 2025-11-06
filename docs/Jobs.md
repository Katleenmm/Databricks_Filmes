# Jobs — Pipeline completo (Landing → Bronze → Silver → Gold)

## Visão geral
Este documento descreve, passo a passo, como criar e configurar os *Jobs* no Databricks para o pipeline do projeto **Databricks_Filmes**. O objetivo é automatizar a execução dos notebooks desde a conexão com o Supabase até a camada **Gold** .

## Notebooks do pipeline (ordem)
1. `Conexao.ipynb` — arquivo de conexão/credenciais (módulo reutilizável)
2. `Filmes 001.ipynb` — Criação de Tabela (Landing)
3. `Filmes 002.ipynb` — Bronze
4. `Filmes 003.ipynb` — Silver
5. `Filmes 004.ipynb` — Gold


---


## 1. Criando o Job no Databricks (UI)

1. No workspace, clique em **Jobs → Create Job**.

2. Dê um nome: `pipeline_filmes_full`.

3. **Task 1**: Ingestão (Notebook task)
   - Type: Notebook
   - Path: `/Workspace/.../Conexao`
   - Cluster: escolha **Existing All-Purpose Cluster** ou **New Job Cluster** (recomendado: Job Cluster)
   - Retry settings: 1 retry (opcional)
   - Timeout: 1h (ajuste conforme necessidade)

4. **Task 2**: landing (Notebook task)
   - Configure como Dependência de Task 1 (set `Depends on` → Task 1)
   - Path: `/Workspace/.../Filmes 001`

5. **Task 3**: bronze (Notebook task)
   - Depend on Task 2

6. **Task 4**: Silver (Notebook task)
   - Depend on Task 3

7. **Task 5**: Gold (Notebook task)
   - Depend on Task 4


8. Salve o Job.

