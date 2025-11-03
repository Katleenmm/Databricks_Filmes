

# Notebooks e Transformações

!!! note "Sequência de notebooks"
    Os notebooks no Databricks seguem a ordem abaixo:

| Ordem | Notebook | Descrição |
|--------|-----------|------------|
| 0️⃣ | `Conexao`    | Cria conexão com o Supabase |
| 1️⃣ | `Filmes_001` | Criação do banco |
| 2️⃣ | `Filmes_002` | Bronze → Silver |
| 3️⃣ | `Filmes_003` | Enriquecimento e joins |
| 4️⃣ | `Filmes_004` | Delta Lake + SCD Type 2 |
| 5️⃣ | `Filmes_005` | Deleta todas as tabelas |

---

## Exemplo de comando Delta

```sql
MERGE INTO silver_filmes AS tgt
USING bronze_filmes AS src
ON tgt.id = src.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *
```

