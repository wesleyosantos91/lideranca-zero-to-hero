Aqui está o README otimizado com o playbook completo, pronto para você entregar ao Devin (ou outro agente de IA). Ele já vem no formato de guia acadêmico e instrutivo, com clareza sobre entradas, saídas e estrutura de arquivos.

⸻

📘 Playbook de Geração de Arquitetura Automatizada

Este documento orienta o Devin a analisar um projeto de software e gerar documentação arquitetural completa (ARCHITECTURE.md), com inventário de tecnologias, APIs, eventos, integrações, fluxos de dados e diagramas.

⸻

🎯 Objetivo
	•	Ler a base de código e artefatos de infraestrutura.
	•	Extrair e organizar informações arquiteturais.
	•	Produzir documentação padronizada e visual.
	•	Garantir clareza para desenvolvedores, arquitetos e gestores.

⸻

🔎 Escopo da Análise
	1.	Stack Tecnológica — linguagens, frameworks, runtime, libs principais.
	2.	Infraestrutura — Docker, K8s, Terraform, pipelines CI/CD.
	3.	APIs — endpoints (HTTP/gRPC), contratos OpenAPI/Swagger.
	4.	Eventos — brokers (Kafka, SQS, RabbitMQ), tópicos, filas, consumidores.
	5.	Dados — bancos, entidades, migrações, índices.
	6.	Integrações — sistemas externos, protocolos, autenticação.
	7.	Resiliência & Operação — circuit breaker, retry, observabilidade.
	8.	Fluxos de Negócio — requisições e eventos.
	9.	Diagramas — C4 e sequência em Mermaid.
	10.	Riscos & Decisões — trade-offs, perguntas abertas.

⸻

📂 Estrutura de Saída Esperada
	•	ARCHITECTURE.md — documento principal (template abaixo).
	•	/docs/inventory.json — inventário técnico detalhado.
	•	/docs/diagrams/*.md — diagramas Mermaid (arquitetura e fluxos).

⸻

📝 Template do ARCHITECTURE.md

# Visão de Arquitetura

## 1. Contexto & Objetivo
Descrição do sistema, domínio e propósito.

## 2. Stack Tecnológica
- Linguagens
- Frameworks
- Infraestrutura
- Observabilidade

## 3. Mapa de Módulos/Serviços
| Módulo | Responsabilidade | Dependências | Deploy |
|--------|------------------|--------------|--------|

## 4. APIs (HTTP/gRPC)
| Método | Path | Responsabilidade | Request | Response | Auth |
|--------|------|------------------|---------|----------|------|

## 5. Eventos (EDA)
| Broker | Canal | Produtor | Consumidor | Esquema | Garantia/DLQ |
|--------|-------|----------|-------------|---------|--------------|

## 6. Dados & Persistência
- Bancos
- Entidades principais
- Estratégias de migração

## 7. Integrações Externas
| Serviço | Protocolo | Auth | SLA | Retry |
|---------|-----------|------|-----|-------|

## 8. Qualidades & Resiliência
- Disponibilidade
- Escalabilidade
- Padrões aplicados

## 9. Diagramas
- C4 (Contexto, Container, Componente)
- Sequência (HTTP)
- Sequência (Evento)

## 10. Decisões & Trade-offs
| Decisão | Motivação | Alternativas | Consequências |

## 11. Operação & Observabilidade
- Logs
- Métricas
- Traces
- Health checks

## 12. Riscos & Mitigações
| Risco | Impacto | Mitigação | Status |

## 13. Roadmap Técnico
- Curto prazo
- Médio prazo
- Longo prazo

## 14. Perguntas Abertas
Itens `TBD` que precisam validação.


⸻

🔄 Etapas do Playbook

Etapa 1 — Inventário
	•	Mapear tecnologias via arquivos (pom.xml, build.gradle, package.json, etc.).
	•	Detectar infra (Dockerfile, terraform/, helm/).
	•	Saída: /docs/inventory.json.

Etapa 2 — APIs
	•	Extrair OpenAPI/Swagger se existir.
	•	Caso contrário, mapear controllers e endpoints.
	•	Preencher tabela no ARCHITECTURE.md.

Etapa 3 — Eventos
	•	Identificar produtores, consumidores e canais (Kafka, SQS, etc.).
	•	Definir garantias (retry, DLQ, ordering).

Etapa 4 — Dados
	•	Levantar bancos, tabelas, entidades.
	•	Descrever estratégias de consistência e transação.

Etapa 5 — Arquitetura & Infra
	•	Mapear camadas (API, domínio, core, adapters).
	•	Relacionar serviços a recursos de infra.

Etapa 6 — Observabilidade
	•	Identificar logs, métricas, traces e painéis.
	•	Verificar health/readiness.

Etapa 7 — Diagramas
	•	Criar diagramas em Mermaid.
	•	Salvar em /docs/diagrams/.

Etapa 8 — Decisões e Riscos
	•	Escrever mini-ADRs.
	•	Listar riscos e mitigação.

Etapa 9 — Revisão
	•	Validar critérios de aceite.
	•	Registrar perguntas abertas.

⸻

📊 Exemplo de Diagrama Mermaid

flowchart LR
Client-->API
API-->ServiceA
ServiceA-->DB[(PostgreSQL)]
ServiceA-- publish -->Kafka[(Topic: events)]
Kafka-- consume -->ServiceB
ServiceB-->Cache[(Redis)]


⸻

✅ Critérios de Aceite
	•	ARCHITECTURE.md completo no template.
	•	Inventário (inventory.json) criado.
	•	Diagramas legíveis em Mermaid.
	•	Perguntas abertas listadas.
	•	Nenhum segredo ou credencial exposta.

⸻

⚡ Como usar no Devin:
Cole este README como instrução. Ele guiará o agente passo a passo na análise do projeto e geração automática da documentação arquitetural.

⸻

Quer que eu já gere uma versão preenchida do ARCHITECTURE.md com placeholders (TBD) para você só revisar e ajustar no projeto?