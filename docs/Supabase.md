# 📘 Supabase — Integração com o Databricks

## 🔹 O que é o Supabase?

O **Supabase** é uma plataforma open-source que oferece uma alternativa ao Firebase, com base em um banco de dados **PostgreSQL**.  
Ele permite criar bancos de dados relacionais, APIs automáticas, autenticação e armazenamento de forma simples e rápida.

No projeto **Databricks_Filmes**, utilizamos o Supabase como **fonte de dados externa** (camada *Landing/Bronze*) para armazenar tabelas relacionadas a filmes e consumi-las no Databricks via **conexão JDBC**.

---

## 1. Criando o Banco de Dados no Supabase

### Passos:

1. Acesse [https://supabase.com](https://supabase.com) e crie uma conta gratuita.
2. No painel inicial, clique em **“New Project”**.
3. Escolha:
   - **Nome do projeto**: `databricks_filmes`
   - **Região**: escolha a mais próxima (ex: *US East 1*)
   - **Senha**: defina uma senha forte (será usada na conexão JDBC)
4. Aguarde a criação do banco (leva cerca de 1 minuto).

Após criado, você terá acesso a:
- Uma **URL de conexão**
- Um **usuário padrão** (ex: `postgres.tofmiplvfvfdzwdaqpqd`)
- A **porta padrão 6543**
- O **driver JDBC** (`org.postgresql.Driver`)

---

## 2. Criando a Tabela no Supabase a partir de um CSV

O Supabase permite criar e popular tabelas automaticamente a partir de arquivos .csv.
Esse método é mais rápido e conveniente quando você já tem dados prontos para importar (como nossa tabela links.csv).

Passos:

1. No painel do Supabase, acesse o menu lateral “Table Editor”.

2. Clique em “New Table” → “Import data from CSV”.

3. Faça upload do arquivo links.csv que contém as colunas movieId, imdbId e tmdbId.

4. O Supabase detectará automaticamente o nome das colunas e tipos de dados.
Confirme se está assim:

movieId → int8

imdbId → text

tmdbId → text

5. Clique em “Import” e aguarde a criação da tabela.

Após a importação, a tabela links ficará disponível no painel do Supabase, pronta para ser acessada via SQL Editor ou pela conexão JDBC com o Databricks.

---

## 3. Conectando o Databricks ao Supabase via JDBC

No Databricks, criamos um **notebook PySpark** para ler essa tabela diretamente do Supabase e salvar uma cópia no Data Lake (camada *Landing*).

### Código completo:

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import current_timestamp, lit

# Inicializar sessão Spark
spark = SparkSession.builder.appName("filmesbronze").getOrCreate()

# Configuração JDBC do Supabase
jdbc_url = (
    "jdbc:postgresql://aws-1-us-east-1.pooler.supabase.com:6543/postgres?sslmode=require"
)

db_properties = {
    "user": "postgres.tofmiplvfvfdzwdaqpqd",    # Usuário gerado pelo Supabase
    "password": "<SUA_SENHA_AQUI>",             # Defina a senha criada no projeto
    "driver": "org.postgresql.Driver",
}

# Ler a tabela 'links' do banco Supabase
df_links = spark.read.jdbc(url=jdbc_url, table="links", properties=db_properties)

display(df_links)

# Caminho de saída no Data Lake (Landing Layer)
output_path = "/Volumes/workspace/landing/dados_filmes/links.csv"

# Gravar o DataFrame como CSV
df_links.write.mode("overwrite").option("header", True).csv(output_path)

print(f"✅ Arquivo CSV salvo com sucesso em: {output_path}")
```

---

## 4. O que o Pipeline faz

| Etapa | Descrição | Camada |
|-------|------------|--------|
| 1️⃣ Conexão | Conecta o Databricks ao banco PostgreSQL hospedado no Supabase via JDBC | *Fonte externa* |
| 2️⃣ Leitura | Lê a tabela `links` (movieId, imdbId, tmdbId) | *Bronze* |
| 3️⃣ Escrita | Grava o conteúdo como arquivo `.csv` no caminho `/Volumes/workspace/landing/dados_filmes/links.csv` | *Landing* |


---

## 🧾 5. Conclusão

Esse pipeline estabelece uma **integração simples e segura entre o Supabase e o Databricks**, permitindo que dados relacionais sejam facilmente ingeridos no seu **Data Lakehouse**.

