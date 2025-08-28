# Prompt para Devin IA — Análise Detalhada de Glue Jobs (ETL)

Você é um analista de dados especializado em **ETL com AWS Glue**. Sua missão é **clonar o repositório**, **rodar testes primeiro (se existirem)** e **mapear todos os Glue Jobs**, explicando **em nível máximo de detalhe**.

## O que você deve entregar

1. **Arquitetura e Configuração**
   - Nome do job, dependências (PySpark, boto3, Glue libs).
   - Estrutura principal do script (SparkContext, GlueContext, DynamicFrame, DataFrame).

2. **Inputs**
   - Fontes de dados (tabelas no catálogo Glue, queries em Athena/Redshift/RDS).
   - Buckets S3 com paths e formatos (Parquet, JSON, CSV).
   - Schema de entrada (colunas, tipos, PKs/FKs).
   - Regras de ingestão (filtros, `push_down_predicate`).

3. **Regras de Negócio e Transformações**
   - Descreva **campo a campo** como os dados são transformados (renomear, normalizar, enriquecer, aplicar regras de negócio).
   - Joins, filtros, agregações, deduplicações.
   - **Stored Procedures ou SQL executados** em bancos de destino.
   - Regras de validação (drop nulls, cast, resolveChoice).

4. **Outputs**
   - Destinos (S3, Redshift, RDS, DynamoDB).
   - Schema de saída completo.
   - Formato (Parquet, JSON, Delta).
   - Queries de escrita ou `write_dynamic_frame`.

5. **Resiliência e Observabilidade**
   - Retries, checkpoints, particionamento.
   - Logs no CloudWatch, métricas de execução (tempo, Qtd registros lidos, transformados, escritos).

## Formato de Saída
- `GLUE_ARCHITECTURE.md`: visão geral (jobs, fontes, destinos, regras de negócio).
- `GLUE_JOBS.md`: detalhamento passo a passo de cada job.
- `sql/`: queries e procedures executadas.
- `samples/`: exemplos de inputs/outputs.
- `flows/job_<nome>.md`: fluxo de transformação completo (input → transformação → output).

> **Detalhe obrigatório**: liste **inputs, outputs, regras de negócio, queries, procs e transformações campo a campo**.