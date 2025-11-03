
# Arquitetura Medalhão

!!! abstract "Conceito"
   A **Arquitetura Medalhão (Medallion Architecture)** organiza os dados em camadas hierárquicas, facilitando o controle de qualidade, versionamento e governança do **Data Lakehouse**.  
   Neste projeto, foi adicionada também a **Camada Landing**, responsável pela **ingestão inicial dos dados brutos** no ambiente Databricks.

---

## Camada Landing
- Primeira área de entrada dos dados no **Data Lake**.  
- Armazena arquivos **exatamente como recebidos da origem**, sem nenhuma transformação.  
- Serve como **backup e histórico de carga original** (CSV, JSON, XML, etc.).  
- Pode ser recriada a qualquer momento para restaurar datasets.  
- Utilizada apenas por engenheiros de dados para ingestão e versionamento inicial.

!!! example "Exemplo de uso"
    - Upload manual de arquivos Kaggle para `dbfs:/FileStore/landing/`  
    - Ingestão automática via API externa (como FIPE, TMDB, etc.)

---

## Camada Bronze
- Recebe dados diretamente da **Landing**.  
- Padroniza formatos (ex.: CSV → Parquet / Delta).  
- Mantém o conteúdo **bruto**, mas com estrutura organizada.   
- É a **base confiável** para reprocessamentos.

!!! note "Exemplo de transformação"
    Conversão de CSVs Kaggle para Delta Tables:
    ```python
    df = spark.read.csv("/mnt/landing/movies_metadata.csv", header=True)
    df.write.format("delta").mode("overwrite").save("/mnt/bronze/movies_metadata")
    ```

---

## Camada Silver
- Contém dados **refinados**, prontos para análises intermediárias.  
- Remove duplicações, trata valores nulos e normaliza colunas.  
- Aplica **joins entre tabelas** (ex.: filmes + elenco + avaliações).  
- Representa o **“data mart técnico”** — estruturado e limpo.

## Camada Gold
- Dados **analíticos e agregados**, prontos para visualizações e BI.  
- Baseia-se nas tabelas Silver e aplica **agregações, métricas e KPIs**.  
- Usada por analistas, cientistas de dados e dashboards.  
- Pode conter tabelas fact/dimension do Data Warehouse.


!!! success "Exemplo de métricas"
    - Top 10 filmes por média de avaliação  
    - Gêneros mais assistidos por década  
    - Média de notas por estúdio  

---



Benefícios: 

- Reduz redundância e inconsistência
- Facilita reprocessamentos
- Implementa histórico (SCD Type 2)
- Mantém rastreabilidade completa