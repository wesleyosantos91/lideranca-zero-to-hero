perfeito! aqui está o conteúdo já no formato do Devin — com os dois primeiros passos iguais aos do print (branch + idioma) e o restante seguindo o playbook v2 que te passei. É só colar no campo Content do “New Playbook”.

⸻

Procedure
	1.	Step 1 — Criação de Branch
	•	Clone o repositório e crie uma nova branch com o prefixo feature/evl.
	•	O restante do nome deve estar em minúsculas e separado por hifens (-).
	•	Exemplos válidos: feature/evl-gerar-arquitetura, feature/evl-docs-arch.
	•	Comandos sugeridos:

git clone <REPO_URL>
cd <REPO_NAME>
git checkout -b "feature/evl-<slug-do-trabalho>"


	•	Aceite: branch criada e ativa (git branch --show-current).

	2.	Step 2 — Idioma (pt-BR)
	•	Todo conteúdo gerado (arquivos, tabelas, descrições) deve ser em português do Brasil.
	•	Palavras reservadas (ex.: nomes de classes, libs, headers) permanecem no idioma original.
	•	Aceite: ARCHITECTURE.md e arquivos em docs/ revisados para pt-BR.
	3.	Step 3 — Inventário (scan do repo)
	•	Detecte serviços/módulos (monorepo-friendly) por buildfiles e artefatos: pom.xml, build.gradle, package.json, go.mod, pyproject.toml, Dockerfile, terraform/*.tf, helm/, k8s/*.yaml, pipelines (.github/workflows/*.yml, Jenkinsfile, etc.).
	•	Gere docs/inventory.json com: repo, revision, generated_at, services[] (language, frameworks, runtimes, infra, apis, events, data, integrations, observability, resilience, security) e open_questions. Use unknown quando não detectado.
	•	Aceite: docs/inventory.json válido e cobrindo ≥90% dos serviços.
	4.	Step 4 — APIs (HTTP/gRPC)
	•	Procure OpenAPI/Swagger (openapi*.y*ml|json, swagger*.json, dependências springdoc).
	•	Se não existir, derive via código (ex.: Spring @GetMapping, @PostMapping, @ControllerAdvice, segurança via @PreAuthorize; Express/Nest routers; Go gin/mux; gRPC .proto).
	•	Preencha em ARCHITECTURE.md → “APIs (HTTP/gRPC)” com colunas: Método | Path | Responsabilidade | Request | Response | Auth | Evidence | Confidence.
	•	Aceite: ≥80% dos endpoints mapeados com Evidence (arquivo:linha).
	5.	Step 5 — Eventos (EDA)
	•	Identifique brokers (Kafka/SQS/Rabbit), canais (tópicos/filas), papéis (produtor/consumidor), esquemas (Avro/JSON/Proto) e garantias (retry, DLQ, ordenação).
	•	Fontes: código + IaC (Terraform, Helm).
	•	Aceite: tabela “Eventos” preenchida com Evidence + Confidence.
	6.	Step 6 — Dados & Persistência
	•	Levante bancos (Postgres/MySQL/Mongo/Redis/Dynamo), entidades (JPA/Mongoose/Prisma/SQLAlchemy), migrações (Flyway/Liquibase/Alembic), índices/constraints.
	•	Documente estratégias de consistência/transação (sagas, outbox, idempotência).
	•	Aceite: stores, 3+ entidades por serviço que usa DB, e migrações identificadas.
	7.	Step 7 — Arquitetura & Infra
	•	Mapeie camadas (API, domínio/core, adapters), e relacione serviço → recursos (deployments/ingress, tópicos/filas, bancos).
	•	Indique pipelines CI/CD e parâmetros de deploy (namespace, replicas, requests/limits).
	•	Aceite: seção “Mapa de Módulos/Serviços” completa com Evidence + Confidence.
	8.	Step 8 — Observabilidade & Operação
	•	Identifique logs (padrão de correlação), métricas (Micrometer/Prometheus/Datadog), traces (OpenTelemetry), health/readiness (/actuator/health, probes).
	•	Liste dashboards/alertas se houver arquivos de regra.
	•	Aceite: pelo menos 1 evidência para logs, métricas, traces ou health por serviço.
	9.	Step 9 — Segurança
	•	Documente AuthN/AuthZ (Spring Security, OAuth/OIDC, JWT, Keycloak), middlewares (CORS/headers).
	•	Sinalize referências a segredos (envs/Secrets Manager/Vault) sem expor valores.
	•	Aceite: seção com políticas/flows de auth e observações de segredos.
	10.	Step 10 — Diagramas (Mermaid)
	•	Gere C4 (Contexto, Container, Componente) e Sequência (1 HTTP + 1 Evento).
	•	Salve em docs/diagrams/ (ex.: c4-context.md, c4-container.md, c4-component-<servico>.md, seq-http-<fluxo>.md, seq-event-<fluxo>.md).
	•	Aceite: arquivos existem e renderizam (blocos ```mermaid).
	11.	Step 11 — Decisões & Riscos
	•	Escreva mini-ADRs (decisão, motivação, alternativas, consequências) e riscos com mitigação.
	•	Aceite: ≥3 decisões e ≥3 riscos com Evidence.
	12.	Step 12 — Template idempotente no ARCHITECTURE.md
	•	Crie/atualize apenas conteúdo entre marcadores:
<!-- BEGIN:GEN:CONTEXT --> ... <!-- END:GEN:CONTEXT --> (um par por seção).
	•	Inclua colunas Evidence e Confidence em todas as tabelas.
	•	Aceite: arquivo segue o template completo (sem seções vazias; use unknown).
	13.	Step 13 — Revisão & Perguntas Abertas
	•	Rode checklist de qualidade (abaixo).
	•	Preencha “Perguntas Abertas” com lacunas, dúvidas e confirmações necessárias.
	•	Aceite: checklist “verde” e perguntas abertas listadas.
	14.	Step 14 — Commit & PR
	•	Adicione arquivos: ARCHITECTURE.md, docs/inventory.json, docs/diagrams/*.md.
	•	Commit padrão:

chore(docs): gerar documentação de arquitetura (inventário, APIs, eventos, diagramas)


	•	Abra pull request com resumo (rubrica de completude + pontos de atenção).
	•	Aceite: PR aberto e link registrado no resumo final.

⸻

Advice & Pointers
	•	Heurísticas por stack
	•	Java/Spring: @SpringBootApplication, @RestController, spring-kafka, spring-cloud-stream, Micrometer, springdoc-openapi, Flyway/Liquibase.
	•	Node: express/nest/fastify, kafkajs, amqplib, aws-sdk (SQS/SNS), pino/winston, opentelemetry.
	•	Go: gin/mux, grpc, kafka-go, aws-sdk-go-v2, zap/logrus, otel.
	•	Python: FastAPI/Flask, sqlalchemy, alembic, pydantic, otel.
	•	Performance em repos grandes
Ignore node_modules/, dist/, build/, target/, .terraform/, venv/, __pycache__/.
Priorize: pom.xml|build.gradle|package.json|go.mod|pyproject.toml|*.proto|*.tf|Dockerfile|docker-compose*.yml|openapi*.y*ml|swagger*.json|*.sql.
	•	Idempotência
Use marcadores BEGIN/END por seção; nunca sobrescreva conteúdo humano fora deles.
	•	Qualidade (gate de saída)
	•	ARCHITECTURE.md completo, pt-BR, sem segredos.
	•	docs/inventory.json válido e cobrindo todos os serviços.
	•	Diagramas renderizam.
	•	Tabelas com Evidence (arquivo:linha) e Confidence (high/medium/low).
	•	Rubrica (0–100): Inventário(25) APIs(20) Eventos(15) Dados(15) Obs(10) Diagramas(10) ADRs&Riscos(5). Meta ≥85.

⸻

Forbidden actions
	•	Expor segredos (tokens, chaves, senhas) ou copiar valores de variáveis sensíveis.
	•	Editar conteúdo fora dos marcadores BEGIN/END no ARCHITECTURE.md.
	•	Apagar docs existentes sem aprovação explícita.
	•	Inventar endpoints/canais/entidades sem Evidence clara.
	•	Comitar artefatos pesados/gerados (builds, .terraform, node_modules).
	•	Alterar configuração de build/deploy sem objetivo desta documentação.

⸻

(Opcional) Snippets úteis para o Devin

Skeleton do inventory.json inicial:

{ "repo": "", "revision": "", "generated_at": "", "services": [], "open_questions": [] }

Bloco Mermaid de exemplo:

flowchart LR
  Client-->API
  API-->ServiceA
  ServiceA-->DB[(PostgreSQL)]
  ServiceA-- publish -->Kafka[(Topic: events)]
  Kafka-- consume -->ServiceB
  ServiceB-->Cache[(Redis)]

se quiser, eu também te entrego um ARCHITECTURE.md já com todos os marcadores para colar direto no repo.