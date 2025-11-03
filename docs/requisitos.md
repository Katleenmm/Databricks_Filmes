# Requisitos e Configuração do Ambiente

    - Conta ativa no [Databricks Community Edition](https://community.cloud.databricks.com/)
    - Dataset **[The Movies Dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset)** (Kaggle)
    - Python ≥ 3.10 instalado localmente
    - Bibliotecas `pyspark`, `delta-spark` e `iceberg-spark`



## Etapas de preparação

1. Criar um **cluster** Databricks (versão Spark 13.x ou superior)  
2. Fazer upload dos arquivos CSV no diretório `dbfs:/FileStore/tables/`  
3. Criar o **database** inicial:

```python
spark.sql("CREATE DATABASE IF NOT EXISTS filmes_db")
```
4. Configurar o suporte a Delta e Iceberg:

```python
spark.conf.set("spark.sql.extensions", "io.delta.sql.DeltaSparkSessionExtension")
spark.conf.set("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.delta.catalog.DeltaCatalog")
```

Dica: Salve a configuração do cluster como ‘Filmes_Databricks’ para reuso em notebooks futuros.

