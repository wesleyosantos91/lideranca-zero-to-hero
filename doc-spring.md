# Prompt para Devin IA — Análise Detalhada de Aplicações Spring (API, Batch, Mensageria)

Você é um engenheiro especializado em **Java 21 + Spring Boot 3.x**. Sua missão é **clonar o repositório**, **executar os testes primeiro** e **mapear todas as jornadas da aplicação**, detalhando **inputs, outputs, regras de negócio, queries, procs e transformações**.

## O que você deve entregar

1. **Arquitetura**
   - Módulos (API, Batch, Workers).
   - Dependências principais.
   - Configurações (application.yml).

2. **Inputs**
   - APIs: payload JSON de entrada (com schema e exemplos reais).
   - Jobs: queries de leitura ou arquivos/fila de entrada.
   - Mensageria: mensagens consumidas (JSON com headers e payload).
   - Schema das tabelas acessadas (colunas, PKs/FKs).

3. **Regras de Negócio e Transformações**
   - Explicar **campo a campo** como os dados de entrada são processados.
   - Regras de validação, cálculos, normalizações.
   - Mapeamento: `input.field → entidade.atributo → coluna DB`.
   - **Queries SQL e Procedures executadas** (texto completo).
   - Processamento em Jobs (reader, processor, writer).
   - Enriquecimento de dados e integrações externas.

4. **Outputs**
   - Inserts, updates, deletes (SQL completo).
   - Mensagens publicadas em filas/tópicos (JSON completo).
   - Chamadas a APIs externas (URL base, método, contrato enviado/recebido).
   - Tabelas e colunas impactadas.

5. **Resiliência e Observabilidade**
   - Retries, timeouts, circuit breaker, DLQ, idempotência.
   - Métricas (Micrometer), logs estruturados, tracing.

## Formato de Saída
- `SPRING_ARCHITECTURE.md`: visão geral da aplicação.
- `JOURNEYS.md`: resumo das jornadas (input → transformação → output).
- `flows/`: arquivos detalhados por jornada (`api_*.md`, `job_*.md`, `queue_*.md`).
- `sql/`: queries e procedures executadas.
- `samples/`: exemplos de payloads de entrada/saída.
- `INVENTORY.json`: inventário de endpoints, jobs, filas, tabelas, queries.
- `DIAGRAMS.md`: fluxos em Mermaid ou C4.

> **Detalhe obrigatório**: cada jornada deve incluir **inputs, outputs, regras de negócio, queries, procedures e transformações campo a campo**.