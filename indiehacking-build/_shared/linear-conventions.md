# Convenções Linear H4NDS

> Referenciado pelas skills quando elas interagem com o Linear via MCP.

## Mapeamento de entidades
- Produto → Initiative
- Projeto → Project
- Milestone → Milestone
- Issue/sub-issue → Issue/sub-issue

## Status (time Engenharia, sub-times herdam)
| Status | Categoria | Quando |
|---|---|---|
| Backlog | Backlog | não priorizado |
| Todo | Unstarted | priorizado, não começado |
| In Progress | Started | em desenvolvimento (automático ao copiar branch) |
| In Review | Started | PR aberto (automático) |
| Staging | Started (NÃO Completed) | merged em develop, em validação |
| Done | Completed | em produção, validada |
| Canceled | Canceled | descartada |
| Duplicate | Canceled | duplicada |

**Regra de ouro: Done = em produção, funcionando e validada.** Staging não conta como completed.

## Automação git → status (só dispara se branch tem ID Linear)
- Cópia da branch → In Progress
- PR review request/activity (develop) → In Review
- Merge em develop → Staging
- Merge em main → Done

Branch sem ID não conecta.

## Templates de issue (Engenharia)
- **Feature** (label Tipo=Feature): seções Artefato Mínimo, Escopo, DoD, Fora de escopo, Notas técnicas
- **Bug** (label Tipo=Bug): seções Ambiente, Passos pra reproduzir, Esperado vs atual, Evidência, Impacto
- **Spike** (label Tipo=Spike): seções Pergunta a responder, Timebox, Output esperado, Critério de conclusão
- **Discovery** (label Tipo=Discovery): seções Pergunta a responder, Por que precisamos saber, O que vou investigar, Output esperado, Critério de conclusão
- **Chore** e **Improvement**: sem template, só título + uma linha

## Labels (Engenharia)
- (G) Tipo: Feature, Bug, Improvement, Chore, Spike, Discovery (exclusivo)
- (L) Tech: Frontend, Backend, Infra, Mobile, Data
- (L) Bloqueado externo
- (L) Dívida técnica

## Prioridade
Campo NATIVO (Urgent/High/Medium/Low/No priority). Nunca duplicar como label.

## SLA (workspace)
- Urgent → 24h
- High → 5 dias úteis
- Medium/Low/No priority → sem SLA

## Project template "Produto zero-to-one"
Milestones:
- **M0 · Fundação** — critério: app sobe em develop com deploy automatizado, dá pra logar
- **M1..Mn** — placeholders, valor demonstrável
- **Lançamento em produção** — primeiros usuários

Issues core dentro de M0:
- Definir e documentar arquitetura base (Spike)
- Provisionar ambientes develop + produção (Chore)
- Pipeline CI/CD (Chore)
- Modelo de dados inicial + migrations (Chore)
- Autenticação e controle de acesso (Feature)
- Observabilidade (Chore)
- Mapear dados pessoais e base legal LGPD (Discovery)
- Implementar controles mínimos LGPD (Chore)

## Fatiamento de tasks
- Vertical: entrega ponta-a-ponta pequena
- Task > 2-3 dias esconde incerteza: quebrar ou virar Spike
- "Trabalhar no backend" não é tarefa válida
