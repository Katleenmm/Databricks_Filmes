Gera um dataframe para cada tabela delta de bronze.

```python
df_credits  = spark.read.format("delta").table("bronze_filmes.credits")
df_keywords = spark.read.format("delta").table("bronze_filmes.keywords")
df_links   = spark.read.format("delta").table("bronze_filmes.links")
df_links_small  = spark.read.format("delta").table("bronze_filmes.links_small")
df_movies_metadata = spark.read.format("delta").table("bronze_filmes.movies_metadata")
df_ratings = spark.read.format("delta").table("bronze_filmes.ratings")
df_ratings_small = spark.read.format("delta").table("bronze_filmes.ratings_small")
```

Adiciona uma nova coluna (metadado) de data e hora de processamento e nome do arquivo de origem.

```python
df_credits  = df_credits.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("credits"))
df_keywords = df_keywords.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("keywords"))
df_links = df_links.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("links"))
df_links_small = df_links_small.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("links_small"))
df_movies_metadata = df_movies_metadata.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("movies_metadata"))
df_ratings = df_ratings.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("ratings"))
df_ratings_small = df_ratings_small.withColumn("data_hora_silver", current_timestamp()).withColumn("nome_tabela", lit("ratings_small"))
```

Salva os dataframes em arquivos delta lake (formato de arquivo) no schema/database "bronze". As tabelas geradas são do tipo MANAGED (gerenciadas). Feito no Filmes 002(anterior).

```python
df_credits.write.format('delta').mode("overwrite").saveAsTable("bronze.credits")
df_keywords.write.format('delta').mode("overwrite").saveAsTable("bronze.keywords")
df_links.write.format('delta').mode("overwrite").saveAsTable("bronze.links")
df_links_small.write.format('delta').mode("overwrite").saveAsTable("bronze.links_small")
df_movies_metadata.write.format('delta').mode("overwrite").saveAsTable("bronze.movies_metadata")
df_ratings.write.format('delta').mode("overwrite").saveAsTable("bronze.ratings")
df_ratings_small.write.format('delta').mode("overwrite").saveAsTable("bronze.ratings_small")
```

Maiusculas, tirando siglas, etc e gravando no formato delta no Silver;
Aplicando Data Quality;

```python
from pyspark.sql import functions as F
import re

def rename_id_columns(colname: str) -> str:
    name = colname.lower() 
    
    if name == 'id':
        return 'movie_id'
    
    if name.endswith('id') and len(name) > 2:
        name = re.sub(r'id$', '_id', name)
        return name
    
    return name

def _safe_drop(df, cols):
    existing = set(df.columns)
    to_drop = [c for c in cols if c in existing]
    return df.drop(*to_drop) if to_drop else df

def renomear_colunas_managed(src_fqn: str, dest_fqn: str = None):
    dest_fqn = dest_fqn or src_fqn
    df = spark.read.format("delta").table(src_fqn)
    
    # Renomeia as colunas já em minúsculo
    new_cols = [rename_id_columns(c) for c in df.columns]
    df = df.toDF(*new_cols)

    # Remove colunas antigas, se existirem
    df = _safe_drop(df, ["data_hora_bronze", "nome_arquivo"])

    # Adiciona colunas de auditoria pedidas
    df = (df
          .withColumn("nome_arquivo_bronze", F.lit(src_fqn))
          .withColumn("data_arquivo_silver", F.current_timestamp())
         )

    # Salva como **Managed Table** (sem LOCATION) — sobrescrevendo destino
    (df.write
       .format("delta")
       .mode("overwrite")       
       .saveAsTable(dest_fqn))

    return dest_fqn
```

```python
renomear_colunas_managed("bronze_filmes.credits",   "silver_filmes.credits")
renomear_colunas_managed("bronze_filmes.keywords",   "silver_filmes.keywords")
renomear_colunas_managed("bronze_filmes.links",     "silver_filmes.links")
renomear_colunas_managed("bronze_filmes.links_small", "silver_filmes.links_small")
renomear_colunas_managed("bronze_filmes.movies_metadata", "silver_filmes.movies_metadata")
renomear_colunas_managed("bronze_filmes.ratings",   "silver_filmes.ratings")
renomear_colunas_managed("bronze_filmes.ratings_small", "silver_filmes.ratings_small")
```

Verifica os dados gravados no formato delta lake tipo MANAGED na camada bronze.

```sql 
SHOW TABLES IN silver_filmes
```

Vendo os detalhes de um tabela delta lake.

```sql
DESCRIBE DETAIL silver_filmes.credits;
```

Mostra se a tabela é MANAGED Ou EXTERNAL.

```sql 
DESCRIBE EXTENDED silver_filmes.links;
```