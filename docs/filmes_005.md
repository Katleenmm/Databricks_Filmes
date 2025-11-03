Criando volumes e schemas/databases.

```sql
-- Apagar todas as tabelas da camada bronze
DROP SCHEMA IF EXISTS workspace.bronze_filmes CASCADE;

-- Apagar todas as tabelas da camada silver
DROP SCHEMA IF EXISTS workspace.silver_filmes CASCADE;

-- Apagar todas as tabelas da camada gold
DROP SCHEMA IF EXISTS workspace.gold_filmes CASCADE;

-- Apagar todas as tabelas e volumes da camada landing
DROP SCHEMA IF EXISTS workspace.landing CASCADE;
```

Confirmar se tudo foi limpo.

```sql
SHOW SCHEMAS IN workspace;
```