index.md

# Projeto Arquitetura Medalhão com Databricks

Neste projeto, adotamos a arquitetura Medalhão como padrão de design de dados para organizar logicamente o lakehouse, aprimorando de forma incremental a estrutura e a qualidade dos dados à medida que eles fluem pelas três camadas de arquitetura — tabelas Bronze, Prata e Ouro.

- **Landing:** ingestão batch, formato bruto, área provisória de chegada.
- **Bronze:** cópia fiel do landing com tipagem mínima e histórico, em Delta.
- **Silver:** limpeza, tratamento, data quality, padronizações e pequenas agregações (Delta).
- **Gold:** regras de negócio, modelo dimensional (ou OBT quando fizer sentido), em Delta.

```text
ADLS (landing CSV) → Databricks
landing.dados → bronze.delta → silver.delta → gold (dim_*, fato_*)
```

**Documentação completa em: [MkDocs]()**

## Requisitos 

* Conta no Azure com um Azure Data Lake Storage Gen2 criado;
* Databricks Community Edition (workspace ativo);
* Dados de filmes em CSV - [The Movies Dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset).


## Primeiros passos
Observação: Volumes exigem Unity Catalog. Se você estiver no Community sem UC, crie apenas schemas e use DBFS (ex.: /FileStore/tables/...) para os arquivos.


### Usando Unity Catalog + Volumes  


1. Ajuste o catálogo conforme seu ambiente (ex.: 'workspace', 'main', etc.)    

```
USE CATALOG workspace;
```

2. Schemas (databases)    
```
CREATE SCHEMA IF NOT EXISTS landing COMMENT 'Schema para dados brutos (landing)';
CREATE SCHEMA IF NOT EXISTS bronze_filmes COMMENT 'Schema para dados bronze (Delta)';
CREATE SCHEMA IF NOT EXISTS silver_filmes COMMENT 'Schema para dados silver (Delta)';
CREATE SCHEMA IF NOT EXISTS gold_filmes   COMMENT 'Schema para dados gold (Delta) - modelagem dimensional';
``` 

3. Volumes (armazenamento gerenciado por schema)
```
CREATE VOLUME IF NOT EXISTS landing.dados_filmes
COMMENT 'Volume para dados brutos (CSV) do domínio filmes';
```







