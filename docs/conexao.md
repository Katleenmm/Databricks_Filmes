
Conecta-se ao Supabase para persistir as tabelas e integra com o Databricks para armazenamento e processamento dos dados.

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import current_timestamp, lit

spark = SparkSession.builder.appName("filmesbronze").getOrCreate()

jdbc_url = (
 "jdbc:postgresql://aws-1-us-east-1.pooler.supabase.com:6543/postgres?sslmode=require"
)

db_properties = {
    "user": #db_user,#     
    "password": #db_password#,         
    "driver": "org.postgresql.Driver",
}

df_links = spark.read.jdbc(url=jdbc_url, table="links", properties=db_properties)

display(df_links)
```

Grava o Dataframe como CSV.

```python

output_path = "/Volumes/workspace/landing/dados_filmes/links.csv"

df_links.write.mode("overwrite").option("header", True).csv(output_path)

print(f"✅ Arquivo CSV salvo com sucesso em: {output_path}")
```