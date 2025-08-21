perfeito — seu playbook já está muito bom. Abaixo deixo uma versão afinada para eficiência do Devin, com guardrails, heurísticas por stack, esquema do inventory.json, critérios de qualidade, e um “prompt drop-in” pronto para colar. Mantive sua estrutura e acrescentei automatizações, fallback paths e sinalização de incerteza.

⸻

Playbook v2 — Geração Automatizada de Arquitetura (para Devin)

🎚️ Modo de operação (regras para o agente)
	•	Estilo: objetivo, técnico, sem jargão desnecessário. Cite caminhos de arquivos quando possível.
	•	Confiabilidade: tudo que for inferência deve trazer confidence ∈ {high, medium, low} + evidence (arquivo/trecho).
	•	Privacidade: nunca exponha segredos ou valores de variáveis sensíveis. Apenas aponte “referência a segredo detectada”.
	•	Idempotência: reexecutar o playbook não deve apagar conteúdo manual. Use blocos marcados (ver “Escrita segura”).
	•	Escalabilidade: para monorepos, trate cada serviço/módulo isoladamente e gere seção/artefatos por serviço.

⸻

🎯 Objetivo

Ler o repositório e produzir:
	1.	ARCHITECTURE.md completo;
	2.	docs/inventory.json (inventário técnico);
	3.	docs/diagrams/*.md (Mermaid p/ C4 + sequência).

⸻

📂 Estrutura de saída (padrão e escrita segura)
	•	ARCHITECTURE.md
	•	Use content markers para seções geradas:
	•	<!-- BEGIN:GEN:SUMMARY --> ... <!-- END:GEN:SUMMARY -->
	•	um marcador por seção do template (ex.: BEGIN:GEN:APIS).
	•	Nunca edite conteúdo fora dos marcadores.
	•	docs/inventory.json (um por repositório; inclua services[] quando monorepo).
	•	docs/diagrams/
	•	c4-context.md, c4-container.md, c4-component-<service>.md
	•	seq-http-<flow>.md, seq-event-<flow>.md

⸻

📝 Template do ARCHITECTURE.md (com marcadores)

Você já tinha; segue com marcadores para idempotência.

	1.	Contexto & Objetivo

<!-- BEGIN:GEN:CONTEXT -->…<!-- END:GEN:CONTEXT -->



	2.	Stack Tecnológica

<!-- BEGIN:GEN:STACK -->…<!-- END:GEN:STACK -->



	3.	Mapa de Módulos/Serviços

<!-- BEGIN:GEN:MODULES -->  


Módulo	Responsabilidade	Dependências	Deploy	Evidence	Confidence


<!-- END:GEN:MODULES -->



	4.	APIs (HTTP/gRPC)

<!-- BEGIN:GEN:APIS -->  


Método	Path	Responsabilidade	Request	Response	Auth	Evidence	Confidence


<!-- END:GEN:APIS -->



	5.	Eventos (EDA)

<!-- BEGIN:GEN:EVENTS -->  


Broker	Canal	Produtor	Consumidor	Esquema	Garantia/DLQ	Evidence	Confidence


<!-- END:GEN:EVENTS -->



	6.	Dados & Persistência

<!-- BEGIN:GEN:DATA -->…<!-- END:GEN:DATA -->



	7.	Integrações Externas

<!-- BEGIN:GEN:INTEGRATIONS -->…<!-- END:GEN:INTEGRATIONS -->



	8.	Qualidades & Resiliência

<!-- BEGIN:GEN:QUALITIES -->…<!-- END:GEN:QUALITIES -->



	9.	Diagramas

<!-- BEGIN:GEN:DIAGRAMS -->links para arquivos em `docs/diagrams`<!-- END:GEN:DIAGRAMS -->



	10.	Decisões & Trade-offs

   <!-- BEGIN:GEN:ADRS -->  


Decisão	Motivação	Alternativas	Consequências	Evidence


   <!-- END:GEN:ADRS -->


	11.	Operação & Observabilidade

   <!-- BEGIN:GEN:OBS -->…<!-- END:GEN:OBS -->


	12.	Riscos & Mitigações

   <!-- BEGIN:GEN:RISKS -->  


Risco	Impacto	Mitigação	Status	Evidence


   <!-- END:GEN:RISKS -->


	13.	Roadmap Técnico

   <!-- BEGIN:GEN:ROADMAP -->…<!-- END:GEN:ROADMAP -->


	14.	Perguntas Abertas

   <!-- BEGIN:GEN:QUESTIONS -->…<!-- END:GEN:QUESTIONS -->



⸻

🔎 Escopo da análise (com heurísticas e fallback)

1) Detecção de serviços e módulos
	•	Sinais de serviço: Dockerfile, helm/, charts/, k8s/, deployment.yaml, serverless.*, main.go, src/main/java, package.json com scripts de start, pyproject.toml, requirements.txt.
	•	Monorepo: diretórios irmãos com buildfiles (pom.xml, build.gradle, package.json, go.mod, pyproject.toml) → cada um vira um item em inventory.services[].

2) Stack Tecnológica (exemplos de padrões por linguagem)
	•	Java/Spring: pom.xml/build.gradle, @SpringBootApplication, @RestController, Resilience4j, OpenFeign, Spring Cloud Stream, Micrometer, OpenTelemetry, Liquibase/Flyway.
	•	Node.js: express, nest, fastify, kafkajs, amqplib, aws-sdk (SQS/SNS), winston/pino, opentelemetry.
	•	Python: FastAPI, Flask, Django, pydantic, sqlalchemy, alembic, opentelemetry.
	•	Go: net/http/gin, grpc, segmentio/kafka-go, aws-sdk-go-v2, zap/logrus, otel.

3) Infra & CI/CD
	•	Container/K8s: Dockerfile, docker-compose.*, k8s/*.yaml, helm/, kustomization.yaml.
	•	IaC: terraform/*.tf (identificar providers, módulos, recursos), cdk*, pulumi*.
	•	Pipelines: .github/workflows/*.yml, Jenkinsfile, .gitlab-ci.yml, azure-pipelines.yml, .circleci/config.yml.
	•	Output: correlacione serviço → recursos (deployment, namespace, tópicos/filas provisionadas via IaC).

4) APIs (HTTP/gRPC) — ordem de extração
	1.	OpenAPI/Swagger: openapi.*.(yml|yaml|json), swagger.*, /docs, springdoc no classpath.
	2.	Derivação por código:
	•	Spring: @RequestMapping/@GetMapping/..., @ControllerAdvice (erros), @PreAuthorize (authz).
	•	Express/Fastify: app.<method>(path, ...), router, guards (Nest).
	•	Go: roteadores (mux, gin), handlers, middlewares.
	•	gRPC: arquivos .proto → serviços/métodos.
	3.	Preencher tabela com: método/path, payloads (modelos/DTOs), Auth (ex.: JWT/OIDC/API Key), Evidence (arquivo:linha).

5) Eventos (EDA)
	•	Brokers: Kafka (propriedades bootstrap.servers, libs spring-kafka, kafkajs, kafka-go), SQS/SNS (SDKs/IaC), RabbitMQ (amqplib, spring-amqp).
	•	Detectar: nomes de tópicos/filas via código e IaC; consumidores/produtores; garantias (DLQ, retry, ordering, exactly-once/quase-sempre “at-least-once”).
	•	Esquemas: Avro/JSON/Protobuf; se houver Schema Registry.

6) Dados & Persistência
	•	Bancos: Postgres, MySQL, Mongo, Redis, DynamoDB (indícios em dependências/IaC/strings de conexão).
	•	Modelagem: JPA @Entity, Mongoose schemas, Prisma, SQLAlchemy.
	•	Migrações: liquibase/, db/changelog, flyway, alembic, prisma/migrations.
	•	Índices e constraints: procure CREATE INDEX, unique, CHECK, FOREIGN KEY.

7) Resiliência & Operação
	•	Resiliência: resilience4j-* (circuit breaker, retry, rate limit, bulkhead), timeouts (HTTP clients), idempotência (chave/lock), DLQs.
	•	Observabilidade: Micrometer/Prometheus, OpenTelemetry (traces), libraries de logs (níveis/padrões de correlação), health/readiness (/actuator/health, probes K8s).
	•	Alertas/Dashboards: arquivos de monitoramento (Datadog/New Relic/Prometheus rules) se existirem.

8) Segurança
	•	AuthN/AuthZ: Spring Security, Keycloak, OAuth/OIDC, JWT libs, helmet/rate-limit (Node).
	•	Segredos: variáveis (*_KEY, *_SECRET, DD_API_KEY etc.). Somente sinalize presença; não logue valores.
	•	Compliance/PII: termos como cpf, ssn, customer_id, encryption libs/KMS.

⸻

📦 docs/inventory.json — Esquema recomendado

{
  "repo": "<nome>",
  "revision": "<git_sha>",
  "generated_at": "<ISO8601>",
  "services": [
    {
      "name": "string",
      "language": "string",
      "frameworks": ["string"],
      "runtimes": ["string"],
      "infra": {
        "containers": true,
        "k8s": {"manifests": ["path"], "namespace": "string"},
        "iac": [{"tool": "terraform", "paths": ["path"]}],
        "pipelines": [{"tool": "github-actions", "files": ["path"]}]
      },
      "apis": [
        {
          "type": "http|grpc",
          "method": "GET|POST|...|N/A",
          "path_or_rpc": "string",
          "request": "schema/ref",
          "response": "schema/ref",
          "auth": "none|jwt|oauth|apikey|mtls|other",
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
          "guarantees": {"dlq": true, "ordering": true, "retries": "int|policy"},
          "evidence": "file:line",
          "confidence": "high|medium|low"
        }
      ],
      "data": {
        "stores": [{"engine": "postgres|mysql|mongo|redis|dynamo|other", "evidence": "file"}],
        "entities": [{"name": "string", "evidence": "file"}],
        "migrations": [{"tool": "flyway|liquibase|alembic|prisma|other", "paths": ["path"]}]
      },
      "integrations": [
        {"name": "string", "protocol": "http|grpc|amqp|sqs|sns|other", "auth": "jwt|oauth|apikey|mtls|none", "sla": "string|unknown", "retry": "policy|unknown", "evidence": "file"}
      ],
      "observability": {
        "logs": {"lib": "string|unknown", "correlation": "trace_id|unknown"},
        "metrics": {"lib": "string|unknown", "exporter": "prometheus|datadog|other"},
        "traces": {"otel": true, "exporter": "otlp|zipkin|jaeger|other"},
        "health": {"paths": ["/actuator/health", "/healthz"], "k8s_probes": true}
      },
      "resilience": {
        "patterns": ["circuit-breaker", "retry", "timeout", "bulkhead", "rate-limit"],
        "libs": ["resilience4j", "other"],
        "idempotency": "present|unknown"
      },
      "security": {
        "auth": ["spring-security", "keycloak", "helmet", "jwt"],
        "secrets_references": true
      }
    }
  ],
  "open_questions": ["string"]
}

Nota: preferir valores unknown em vez de omissão.

⸻

🔄 Etapas (com critérios e ações automatizadas)

Etapa 1 — Inventário
	•	Mapear stacks, infra, pipelines e integradores por serviço.
	•	Saída: docs/inventory.json preenchido.
	•	Aceite: services[].language e frameworks[] sem unknown para ≥90% dos serviços.

Etapa 2 — APIs
	•	Extrair OpenAPI; se não houver, derivar por código (regras acima).
	•	Validar endpoints representativos (≥80% cobertos).
	•	Aceite: tabela em ARCHITECTURE.md + evidências.

Etapa 3 — Eventos
	•	Levantar canais, papéis (produtor/consumidor), DLQs e retries (por IaC + código).
	•	Aceite: tabela consolidada + garantias explícitas.

Etapa 4 — Dados
	•	Identificar stores, entidades principais e migrações; índices/constraints se houver.
	•	Aceite: stores e migrações mapeados; pelo menos 3 entidades por serviço que tenha DB.

Etapa 5 — Arquitetura & Infra
	•	Correlacionar módulos ↔ recursos (deployments, tópicos/filas, bancos).
	•	Aceite: seção “Mapa de Módulos/Serviços” completa.

Etapa 6 — Observabilidade
	•	Logs, métricas, traces, health/readiness, dashboards/regras.
	•	Aceite: pelo menos 1 evidência por dimensão (logs/metrics/traces/health).

Etapa 7 — Diagramas (Mermaid)
	•	Gerar C4 (Contexto/Container/Componente) e 2 sequências (HTTP + Evento).
	•	Aceite: arquivos em docs/diagrams com links na seção 9.

Etapa 8 — Decisões e Riscos
	•	Mini-ADRs (decisões detectadas) + riscos técnicos com mitigação.
	•	Aceite: ≥3 decisões e ≥3 riscos com evidências.

Etapa 9 — Revisão
	•	Preencher “Perguntas Abertas”; rodar checklist de qualidade (abaixo).

⸻

✅ Checklist de Qualidade (gate de saída)
	•	ARCHITECTURE.md sem seções vazias (apenas “unknown” quando necessário).
	•	docs/inventory.json válido (JSON), com services.length ≥ 1.
	•	Todos os trechos gerados cercados por marcadores BEGIN/END.
	•	Nenhum segredo/valor sensível impresso.
	•	Diagramas Mermaid renderizam (blocos ```mermaid).
	•	Tabelas contêm coluna Evidence e Confidence.
	•	Monorepo: todos os serviços com entrada no inventário.

⸻

🧪 Rubrica de completude (0–100)
	•	Inventário (25)
	•	APIs (20)
	•	Eventos (15)
	•	Dados (15)
	•	Observabilidade (10)
	•	Diagramas (10)
	•	ADRs & Riscos (5)

Meta: ≥ 85.

⸻

🗺️ Modelos de Diagramas (Mermaid)

C4 — Contexto (exemplo):

graph LR
  Client((Usuário)) -->|HTTP| API[API Gateway/Service]
  API --> SvcA[Serviço A]
  SvcA --> DB[(PostgreSQL)]
  SvcA -->|produce| Topic[(Kafka topic: events)]
  Topic -->|consume| SvcB[Serviço B]
  SvcB --> Cache[(Redis)]

Sequência — HTTP (exemplo):

sequenceDiagram
  participant C as Client
  participant A as API
  participant S as ServiceA
  participant DB as PostgreSQL
  C->>A: POST /orders
  A->>S: createOrder(payload)
  S->>DB: INSERT order
  DB-->>S: ok
  S-->>A: 201 Created
  A-->>C: 201 {orderId}

Sequência — Evento (exemplo):

sequenceDiagram
  participant S as ServiceA
  participant K as Kafka (orders)
  participant B as ServiceB
  S->>K: publish OrderCreated
  K-->>B: OrderCreated
  B->>B: process & retry policy


⸻

🧰 Filtros de performance (repos grandes)
	•	Ignore diretórios: node_modules/, dist/, build/, target/, .terraform/, venv/, __pycache__/.
	•	Amostragem de arquivos: priorize **/*(pom.xml|build.gradle|package.json|go.mod|pyproject.toml|*.proto|*.tf|Dockerfile|docker-compose*.yml|openapi*.yaml|swagger*.json|*.sql).
	•	Para APIs sem OpenAPI, limite a 200 endpoints por serviço (amostrar por pasta/feature).

⸻

🛡️ Segurança & Compliance (check automático)
	•	Sinalize presença de: PII (cpf/ssn/email), encryption, refs KMS/Secrets Manager/HashiCorp Vault.
	•	Liste middlewares de segurança (CORS, CSRF, headers) e autenticação.

⸻

📌 “Prompt drop-in” (cole no Devin)

Objetivo
Analise o repositório atual e gere documentação arquitetural padronizada. Produza:
	•	ARCHITECTURE.md com o template e marcadores BEGIN/END;
	•	docs/inventory.json conforme o esquema abaixo;
	•	Diagramas Mermaid em docs/diagrams/*.

Regras
	•	Não exponha segredos. Informe apenas “referência a segredo detectada”.
	•	Sempre inclua evidence (arquivo:linha) e confidence em tabelas.
	•	Seja idempotente: edite apenas entre marcadores.
	•	Para monorepos, trate cada serviço/módulo separadamente.

Como fazer (pipeline):
	1.	Detecte serviços por buildfiles e artefatos de deploy (Docker/K8s).
	2.	Preencha docs/inventory.json (esquema abaixo).
	3.	APIs: extraia OpenAPI; se ausente, derive de anotações/roteadores; inclua auth.
	4.	Eventos: mapeie produtores/consumidores/canais e garantias (DLQ/retry).
	5.	Dados: bancos, entidades principais, migrações, índices.
	6.	Observabilidade: logs/metrics/traces/health.
	7.	Segurança: authN/Z, referências a segredos, middlewares.
	8.	Diagramas: gere C4 (context/container/component) e 2 sequências (HTTP e Evento).
	9.	Decisões e riscos: escreva mini-ADRs e riscos com mitigação.
	10.	Preencha “Perguntas Abertas” com lacunas e incertezas.

Esquema docs/inventory.json:
(cole exatamente este JSON como base do arquivo, preenchendo os campos e mantendo unknown quando necessário)

{ "repo": "", "revision": "", "generated_at": "", "services": [], "open_questions": [] }

Em seguida, substitua pelo esquema completo abaixo e preencha (ver seção “docs/inventory.json — Esquema recomendado” deste playbook).

Checklist de qualidade (obrigatório cumprir):
	•	ARCHITECTURE.md sem seções vazias (use unknown quando não detectado).
	•	Tabelas com Evidence e Confidence.
	•	Diagramas renderizáveis.
	•	Zero segredos expostos.
	•	Para cada serviço: pelo menos 1 evidência de logs/metrics/traces ou health.

Saídas finais:
	•	ARCHITECTURE.md (todas as seções preenchidas);
	•	docs/inventory.json válido;
	•	docs/diagrams/*.md (C4 e duas sequências).

⸻

🔧 Pequenos acréscimos que elevam a eficácia
	•	Colunas “Evidence/Confidence” nas tabelas → auditabilidade e transparência.
	•	Marcadores BEGIN/END → idempotência e convivência com edição humana.
	•	Rubrica de completude → fácil checagem de pronto-para-uso.
	•	Heurísticas por stack → melhora recall quando não há OpenAPI/IaC limpos.
	•	Sinalização de segredos sem valores → segurança por padrão.
	•	Monorepo-first → documentação que escala bem em projetos modernos.

Se quiser, eu já instancio esse playbook em um ARCHITECTURE.md esqueleto com os marcadores e preparo o docs/inventory.json base para você colar no repositório.