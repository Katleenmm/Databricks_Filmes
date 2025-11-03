```sql

CREATE SCHEMA IF NOT EXISTS workspace.landing
COMMENT 'Schema/Database para dados bronze (delta)';

CREATE VOLUME IF NOT EXISTS workspace.landing.dados_filmes
COMMENT 'Volume para dados brutos criados no schema/database landing';

CREATE SCHEMA IF NOT EXISTS workspace.bronze_filmes
COMMENT 'Schema/Database para dados bronze (delta)';

CREATE SCHEMA IF NOT EXISTS workspace.silver_filmes
COMMENT 'Schema/Database para dados silver (delta)';

CREATE SCHEMA IF NOT EXISTS workspace.gold_filmes
COMMENT 'Schema/Database para dados gold (delta) - modelagem dimensional';
```