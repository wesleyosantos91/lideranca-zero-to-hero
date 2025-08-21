valeu pelo feedback! aqui vai uma versão mais completa e robusta, já no formato do Devin e pronta para colar no “New Playbook”.
Inclui: passos detalhados (com os dois primeiros conforme suas imagens), critérios de aceite, templates prontos (ARCHITECTURE.md com marcadores, inventory.json com schema), comandos úteis por stack, heurísticas de extração (APIs, eventos, dados, IaC), rubrica de qualidade, checklist final e modelo de PR.

⸻

Procedure
	1.	Step 1 — Criação de Branch
	•	Clone o repositório e crie uma branch prefixada com feature/evl; restante do nome minúsculo e com hífens.
	•	Ex.: feature/evl-gerar-arquitetura, feature/evl-docs-arch.
	•	Comandos:

git clone <REPO_URL> && cd <REPO_NAME>
git checkout -b "feature/evl-<slug-do-trabalho>"
git branch --show-current   # deve mostrar a nova branch


	•	Aceite: branch ativa com o prefixo correto.

	2.	Step 2 — Idioma (pt-BR)
	•	Todo conteúdo gerado (títulos, textos, tabelas, descrições) deve estar em português do Brasil.
	•	Termos técnicos (nomes de classes, libs, headers) podem ficar no idioma original.
	•	Aceite: ARCHITECTURE.md e docs/* revisados para pt-BR.
	3.	Step 3 — Varredura & Inventário do Repositório
	•	Meta: criar docs/inventory.json cobrindo 100% dos serviços/módulos identificáveis.
	•	Detectar serviços/módulos (monorepo-friendly) por presença de:
	•	Buildfiles: pom.xml, build.gradle*, package.json, go.mod, pyproject.toml, setup.cfg, .csproj.
	•	Artefatos de entrega: Dockerfile, docker-compose*.yml, helm/, k8s/*.y*ml, serverless.*, Procfile.
	•	IaC: terraform/**/*.tf, cdk*, pulumi*.
	•	Pipelines: .github/workflows/*.yml, Jenkinsfile, .gitlab-ci.yml, azure-pipelines.yml, .circleci/config.yml.
	•	Ignorar diretórios pesados: node_modules/, dist/, build/, target/, .terraform/, venv/, __pycache__/, coverage/.
	•	Preencher docs/inventory.json com o esquema ao final deste playbook (use unknown quando necessário).
	•	Aceite: inventory.json válido, com services[].language e frameworks[] ≠ unknown em ≥90% dos serviços.
	4.	Step 4 — Preparar ARCHITECTURE.md (template idempotente)
	•	Criar (ou atualizar) ARCHITECTURE.md com marcadores por seção, p.ex.:
<!-- BEGIN:GEN:APIS --> … <!-- END:GEN:APIS -->.
	•	Nunca editar conteúdo humano fora dos marcadores.
	•	Colunas obrigatórias nas tabelas: Evidence (arquivo:linha) e Confidence (high|medium|low).
	•	Aceite: arquivo completo com todas as seções (pode usar unknown).
	5.	Step 5 — Extração de APIs (HTTP/gRPC)
	•	Preferência: OpenAPI/Swagger (openapi*.y*ml|json, swagger*.json) ou dependências (springdoc-openapi, Swashbuckle, fastapi.openapi, NestJS Swagger).
	•	Fallback por stack:
	•	Spring (Java): anotações @RequestMapping|@Get|@Post|@Put|@Delete, @ControllerAdvice (erros), @PreAuthorize/SecurityFilterChain (authz).
	•	Busca sugerida (regex aproximada): @(Get|Post|Put|Delete|Patch)Mapping\\(.*path\\s*=\\s*\"([^\"]+)\".*\\).
	•	Node/Express/Nest/Fastify: app.<verb>(path, ...), router.<verb>, decorators @Get('/path').
	•	Go: roteadores gin/mux/chi, r.HandleFunc("/path", ...), grpcrand/*.proto p/ gRPC.
	•	Python/FastAPI/Flask: @app.get('/path'), @router.post(...).
	•	gRPC: ler .proto (services, rpcs, messages).
	•	Documentar na tabela “APIs (HTTP/gRPC)” (Método, Path, Responsabilidade, Request, Response, Auth, Evidence, Confidence).
	•	Aceite: ≥80% dos endpoints mapeados com Evidence.
	6.	Step 6 — Eventos (EDA)
	•	Detectar brokers, canais e papéis:
	•	Kafka: libs spring-kafka, kafkajs, kafka-go; propriedades (bootstrap.servers, spring.kafka.*); IaC (aws_msk_cluster, tópicos via kafkacat/scripts).
	•	SQS/SNS: SDKs AWS (aws-sdk, aws-java-sdk-sqs/v2), IaC aws_sqs_queue, aws_sns_topic.
	•	RabbitMQ: spring-amqp, amqplib.
	•	Extrair nomes de tópicos/filas, esquemas (Avro/JSON/Proto), garantias (DLQ, retry, ordering, exactly/at least once), e produtores/consumidores.
	•	Aceite: tabela “Eventos (EDA)” completa com Evidence + Confidence.
	7.	Step 7 — Dados & Persistência
	•	Identificar engines: Postgres/MySQL/Mongo/Redis/Dynamo, por dependências, strings de conexão e IaC.
	•	Mapear entidades (JPA @Entity, Mongoose schemas, Prisma, SQLAlchemy), migrações (Flyway/Liquibase/Alembic/Prisma), índices e constraints.
	•	Descrever consistência/transação: outbox, sagas, idempotência, isolamento, TTL/expiração (cache).
	•	Aceite: stores + migrações + ≥3 entidades chave por serviço que usa DB.
	8.	Step 8 — Arquitetura & Infra
	•	Camadas: API, domínio/core, adapters/infra; relacionar serviço → recursos (deploy, ingress, tópicos/filas, bancos).
	•	K8s: namespace, deployments, replicas, HPA, requests/limits; health/readiness probes.
	•	Pipelines: build/test/lint/quality gates, estratégias de versionamento e promoção (dev→qa→prod).
	•	Aceite: seção “Mapa de Módulos/Serviços” + diagrama de containers com links.
	9.	Step 9 — Observabilidade & Operação
	•	Logs: libs (pino/winston/logback), correlação (trace_id, span_id), formatação (JSON), retenção.
	•	Métricas: Micrometer/Prometheus/Datadog; métricas de negócio vs. técnicas.
	•	Traces: OpenTelemetry (exporter OTLP/Jaeger/Zipkin), sampling.
	•	Health: /actuator/health (Spring), /healthz, liveness/readiness probes, SLIs/SLOs se existir.
	•	Aceite: pelo menos 1 evidência para logs, métricas, traces ou health por serviço.
	10.	Step 10 — Segurança
	•	AuthN/AuthZ: Spring Security/Keycloak/OAuth2/OIDC/JWT; em Node, passport/oidc, middlewares.
	•	Segredos: variáveis e managers (AWS Secrets Manager, Vault, KMS) — não expor valores, apenas sinalizar referências.
	•	Superfície: CORS, headers de segurança, rate limiting, TLS/MTLS, políticas de acesso a tópicos/filas/DB.
	•	Aceite: seção preenchida com fluxos de auth e controles de segredos.
	11.	Step 11 — Diagramas (Mermaid)
	•	Criar em docs/diagrams/:
	•	c4-context.md, c4-container.md, c4-component-<servico>.md
	•	seq-http-<fluxo>.md, seq-event-<fluxo>.md
	•	Modelos prontos na seção “Snippets” abaixo.
	•	Aceite: arquivos existem e renderizam (```mermaid).
	12.	Step 12 — Decisões & Riscos
	•	Produzir mini-ADRs (Decisão, Motivação, Alternativas, Consequências, Evidence).
	•	Riscos técnicos (Impacto, Probabilidade, Mitigação, Status).
	•	Aceite: ≥3 decisões e ≥3 riscos com Evidence.
	13.	Step 13 — Perguntas Abertas & Roadmap
	•	Preencher lacunas, dependências externas, premissas a validar.
	•	Roadmap técnico em curto/médio/longo prazo.
	•	Aceite: seção preenchida, sem TODOs órfãos.
	14.	Step 14 — Commit & Pull Request
	•	Adicionar novos arquivos:
ARCHITECTURE.md, docs/inventory.json, docs/diagrams/*.md.
	•	Commit:

chore(docs): gerar documentação de arquitetura (inventário, APIs, eventos, dados, diagramas)


	•	PR description (template):
	•	Objetivo & Escopo
	•	Sumário da cobertura (inventário, APIs %, eventos %, dados)
	•	Rubrica de completude (0–100)
	•	Riscos mapeados
	•	Perguntas abertas
	•	Checklist de qualidade ✅
	•	Aceite: PR aberto e checklist “verde”.

⸻

Advice & Pointers

Heurísticas rápidas por stack
	•	Spring/Java: procurar @SpringBootApplication, @RestController, @KafkaListener, Resilience4j, Micrometer, springdoc, Flyway/Liquibase, Feign/WebClient, @Transactional.
	•	Node/Nest/Express: examine controllers, routes, guard/interceptors, kafkajs/amqplib, pino/winston, opentelemetry.
	•	Go: gin/mux/chi, grpc, kafka-go, aws-sdk-go-v2/sqs, zap/logrus, otel.
	•	Python/FastAPI: decorators de rota, Pydantic models, sqlalchemy/alembic, opentelemetry.

Mapeamento IaC → serviços
	•	Terraform: aws_ecs_service, kubernetes_deployment, aws_msk_cluster, aws_sqs_queue, aws_sns_topic, aws_db_instance/cluster, helm_release.
	•	Relacione por naming (labels/annotations), imagens (tag do container), variáveis de ambiente (URLs de topics/filas/DB).

Evidência & Confiança (como preencher)
	•	Evidence: path/arquivo:linha (ou bloco com range).
	•	Confidence:
	•	high → apontado por contrato oficial (OpenAPI/.proto) ou IaC inequívoco.
	•	medium → inferido de código + config coerentes.
	•	low → dedução com lacunas (sem contrato/infra claros).

Performance em repositórios grandes
	•	Faça scan dirigido por extensão/nomes prioritários.
	•	Para APIs sem contrato e com muitos endpoints, amostrar até 200 rotas por serviço (representativas por domínio).

Rubrica de completude (0–100)
	•	Inventário (25)
	•	APIs (20)
	•	Eventos (15)
	•	Dados (15)
	•	Observabilidade (10)
	•	Diagramas (10)
	•	ADRs & Riscos (5)
Meta: ≥ 85.

Checklist de qualidade
	•	Conteúdo em pt-BR (exceto termos reservados).
	•	ARCHITECTURE.md completo com marcadores BEGIN/END.
	•	Tabelas com Evidence e Confidence.
	•	docs/inventory.json válido e cobrindo todos os serviços.
	•	Diagramas Mermaid renderizam.
	•	Zero segredos expostos.
	•	Monorepo: todos os serviços catalogados.

⸻

Forbidden actions
	•	Expor segredos (tokens, chaves, credenciais) — somente sinalizar referências.
	•	Escrever fora dos marcadores BEGIN/END no ARCHITECTURE.md.
	•	Remover documentação pré-existente sem aprovação explícita.
	•	“Inventar” endpoints/canais/entidades sem Evidence.
	•	Comitar artefatos binários/pesados (builds, .terraform, node_modules).

⸻

Snippets (prontos para colar)

1) ARCHITECTURE.md — esqueleto com marcadores

# Visão de Arquitetura

## 1. Contexto & Objetivo
<!-- BEGIN:GEN:CONTEXT -->
unknown
<!-- END:GEN:CONTEXT -->

## 2. Stack Tecnológica
<!-- BEGIN:GEN:STACK -->
- Linguagens: …
- Frameworks: …
- Infraestrutura: …
- Observabilidade: …
<!-- END:GEN:STACK -->

## 3. Mapa de Módulos/Serviços
<!-- BEGIN:GEN:MODULES -->
| Módulo | Responsabilidade | Dependências | Deploy | Evidence | Confidence |
|---|---|---|---|---|---|
<!-- END:GEN:MODULES -->

## 4. APIs (HTTP/gRPC)
<!-- BEGIN:GEN:APIS -->
| Método | Path | Responsabilidade | Request | Response | Auth | Evidence | Confidence |
|---|---|---|---|---|---|---|---|
<!-- END:GEN:APIS -->

## 5. Eventos (EDA)
<!-- BEGIN:GEN:EVENTS -->
| Broker | Canal | Produtor | Consumidor | Esquema | Garantia/DLQ | Evidence | Confidence |
|---|---|---|---|---|---|---|---|
<!-- END:GEN:EVENTS -->

## 6. Dados & Persistência
<!-- BEGIN:GEN:DATA -->
- Bancos: …
- Entidades principais: …
- Migrações: …
- Índices/Constraints: …
<!-- END:GEN:DATA -->

## 7. Integrações Externas
<!-- BEGIN:GEN:INTEGRATIONS -->
| Serviço | Protocolo | Auth | SLA | Retry | Evidence | Confidence |
|---|---|---|---|---|---|---|
<!-- END:GEN:INTEGRATIONS -->

## 8. Qualidades & Resiliência
<!-- BEGIN:GEN:QUALITIES -->
- Disponibilidade: …
- Escalabilidade: …
- Padrões aplicados: circuit-breaker, retry, timeout, bulkhead, rate-limit, idempotência, outbox.
<!-- END:GEN:QUALITIES -->

## 9. Diagramas
<!-- BEGIN:GEN:DIAGRAMS -->
- C4 (Contexto/Container/Componente): ver `docs/diagrams/*`.
- Sequência (HTTP e Evento): ver `docs/diagrams/*`.
<!-- END:GEN:DIAGRAMS -->

## 10. Decisões & Trade-offs
<!-- BEGIN:GEN:ADRS -->
| Decisão | Motivação | Alternativas | Consequências | Evidence |
|---|---|---|---|---|
<!-- END:GEN:ADRS -->

## 11. Operação & Observabilidade
<!-- BEGIN:GEN:OBS -->
- Logs: …
- Métricas: …
- Traces: …
- Health checks: …
<!-- END:GEN:OBS -->

## 12. Riscos & Mitigações
<!-- BEGIN:GEN:RISKS -->
| Risco | Impacto | Mitigação | Status | Evidence |
|---|---|---|---|---|
<!-- END:GEN:RISKS -->

## 13. Roadmap Técnico
<!-- BEGIN:GEN:ROADMAP -->
- Curto prazo: …
- Médio prazo: …
- Longo prazo: …
<!-- END:GEN:ROADMAP -->

## 14. Perguntas Abertas
<!-- BEGIN:GEN:QUESTIONS -->
- …
<!-- END:GEN:QUESTIONS -->

2) docs/inventory.json — schema recomendado

{
  "repo": "",
  "revision": "",
  "generated_at": "",
  "services": [
    {
      "name": "string",
      "language": "string",
      "frameworks": ["string"],
      "runtimes": ["string"],
      "infra": {
        "containers": true,
        "k8s": {"manifests": ["path"], "namespace": "string", "hpa": "present|unknown"},
        "iac": [{"tool": "terraform|cdk|pulumi", "paths": ["path"]}],
        "pipelines": [{"tool": "github-actions|jenkins|gitlab|azure", "files": ["path"]}]
      },
      "apis": [
        {
          "type": "http|grpc",
          "method": "GET|POST|...|N/A",
          "path_or_rpc": "string",
          "request": "schema/ref|unknown",
          "response": "schema/ref|unknown",
          "auth": "none|jwt|oauth|apikey|mtls|unknown",
          "evidence": "file:line",
          "confidence": "high|medium|low"
        }
      ],
      "events": [
        {
          "broker": "kafka|sqs|rabbit|sns|other",
          "channel": "string",
          "role": "producer|consumer|both",
          "schema": "avro|json|proto|unknown",
          "guarantees": {"dlq": true, "ordering": false, "retries": "int|policy|unknown"},
          "evidence": "file:line",
          "confidence": "high|medium|low"
        }
      ],
      "data": {
        "stores": [{"engine": "postgres|mysql|mongo|redis|dynamo|other", "evidence": "file"}],
        "entities": [{"name": "string", "evidence": "file"}],
        "migrations": [{"tool": "flyway|liquibase|alembic|prisma|other", "paths": ["path"]}],
        "indexes": [{"name": "string", "evidence": "file"}]
      },
      "integrations": [
        {"name": "string", "protocol": "http|grpc|amqp|sqs|sns|other", "auth": "jwt|oauth|apikey|mtls|none", "sla": "string|unknown", "retry": "policy|unknown", "evidence": "file"}
      ],
      "observability": {
        "logs": {"lib": "string|unknown", "correlation": "trace_id|unknown"},
        "metrics": {"lib": "micrometer|prometheus|datadog|other|unknown", "exporter": "prometheus|dogstatsd|otlp|unknown"},
        "traces": {"otel": true, "exporter": "otlp|zipkin|jaeger|unknown"},
        "health": {"paths": ["/actuator/health", "/healthz"], "k8s_probes": true}
      },
      "resilience": {
        "patterns": ["circuit-breaker","retry","timeout","bulkhead","rate-limit"],
        "libs": ["resilience4j","hystrix","other","unknown"],
        "idempotency": "present|unknown"
      },
      "security": {
        "auth": ["spring-security","keycloak","oauth2","jwt","helmet","unknown"],
        "secrets_references": true
      }
    }
  ],
  "open_questions": ["string"]
}

3) Modelos de diagramas Mermaid

C4 — Contexto

```mermaid
graph LR
  Client((Usuário)) -->|HTTP| API[API Gateway/Service]
  API --> SvcA[Serviço A]
  SvcA --> DB[(PostgreSQL)]
  SvcA -->|publish| Topic[(Kafka: events)]
  Topic -->|consume| SvcB[Serviço B]
  SvcB --> Cache[(Redis)]

**C4 — Container**
```md
```mermaid
graph TB
  subgraph Cluster K8s [Cluster Kubernetes]
    api(API Service Pod):::svc --> db[(PostgreSQL PVC)]:::db
    api --> broker[(Kafka/MSK)]:::msg
    api --> ext[(Serviço Externo)]:::ext
  end
  classDef svc stroke-width:1px;
  classDef db stroke-width:1px;
  classDef msg stroke-width:1px;
  classDef ext stroke-width:1px;

**Sequência — HTTP**
```md
```mermaid
sequenceDiagram
  participant C as Client
  participant A as API
  participant S as ServiceA
  participant D as DB
  C->>A: POST /orders
  A->>S: createOrder(payload)
  S->>D: INSERT order
  D-->>S: ok
  S-->>A: 201 Created
  A-->>C: 201 {orderId}

**Sequência — Evento**
```md
```mermaid
sequenceDiagram
  participant P as Producer (SvcA)
  participant K as Kafka (orders)
  participant C as Consumer (SvcB)
  P->>K: OrderCreated
  K-->>C: OrderCreated
  C->>C: process (retry/backoff, idempotency key)
