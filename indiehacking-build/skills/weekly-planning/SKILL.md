---
name: weekly-planning
description: |
  Sessão semanal de priorização: lê estado do produto, retrospectiva da
  semana passada, captura mudanças de cabeça (decisões/premissas),
  prioriza issues pra semana, captura tasks novas durante a sessão,
  consolida tudo em 1 ponto de aprovação. Use quando o autor falar "vamos
  planejar a semana", "o que faço essa semana", "planning session",
  "review da semana passada".
---

# weekly-planning

Sessão estruturada de planning. 6 passos fixos.

## Ativação

Triggers:
- "vamos planejar a semana"
- "o que faço essa semana"
- "planning session"
- "review da semana passada"
- "vou começar a semana"

## Procedimento (ordem fixa)

### Passo 1 — Foto atual

Invoque internamente o procedimento de `progress-monitor`. Mostre a foto em 1 tela.

### Passo 2 — Retrospectiva (se não for primeira sessão)

- `git log --since="7 days ago" --oneline` no repo de código associado (se conhecido) e no repo do cérebro
- Cruze com issues que estavam em Todo na semana passada (use a tag de sessão anterior, ou apenas filtre issues que mudaram pra Done na última semana)
- Mostre: "Você planejou X issues, completou Y, ficou Z em andamento."

Se não houver sessão anterior detectável, pule.

### Passo 3 — Mudanças desde a última sessão

Pergunte ao autor:
- "O que mudou na sua cabeça desde semana passada? Premissa que caiu, decisão pendente, algo do mercado?"

Se na resposta ele explicitar decisão/premissa, ative `register-decision` ou `capture-assumption` em paralelo (mas sem committar ainda — segura o commit pro fechamento).

Quando ativar essas skills aqui, instrua-as a SÓ compor a entrada (mostrar preview, pedir aprovação do CONTEÚDO), mas NÃO committar — a entrada aprovada vai pra lista interna `mudancas_cerebro` que será commitada junto no Passo 6.

### Passo 4 — Priorização da semana

Liste issues elegíveis (Backlog + Todo do project ativo). Mostre prioridade atual, milestone, tamanho estimado se houver.

Pergunte:
- "Quais sobem pro Todo da semana? Máximo 5 (mais que isso é fantasia em projeto solo). Em que ordem?"

Compare a soma de estimativas com tempo disponível na semana (autor informa hours/week). Se >100% da capacidade, avise mas deixe o autor decidir.

### Passo 5 — Tasks novas durante a sessão

Mantenha uma lista interna `novas_tasks = []` ao longo de toda a sessão. Quando aparecer:
- "ah, vou precisar fazer X também"
- "encontrei um bug em Y"
- "preciso de uma migration antes"

Acrescente à lista (sem criar ainda — não polui Linear durante a conversa).

### Passo 6 — Fechamento (PONTO DE APROVAÇÃO ÚNICO)

Mostre ao autor um sumário consolidado:

> **Sessão de planning — YYYY-MM-DD** (use a data atual: rode `date +%Y-%m-%d` se precisar)
>
> **Mutações no Linear:**
> - Subir pra Todo (em ordem): #LIV-127, #LIV-130, #LIV-131
> - Ajustar prioridade: #LIV-128 → High (era No priority)
> - Mover pra Backlog: #LIV-129 (foi despriorizado)
> - Criar novas issues:
>   1. [Bug · No priority] Email transacional não chega em domínio @hotmail
>   2. [Chore · Low] Adicionar retry com backoff no webhook Stripe
>   3. [Spike · 1 dia] Investigar quotas de envio do Resend
>
> **Mutações no cérebro:**
> - DECISIONS.md: 1 nova decisão (X), 1 update de premissa (Y validou)
>
> Tudo certo? Aplico agora?

Se sim, aplicar tudo em sequência via MCP + commits locais.

### Alerta anti-sobrebuild (opcional)

Após passo 1, se detectar:
- Várias issues mergeadas em milestones que NÃO são "Lançamento em produção"
- Nenhum sinal de campanha ativa (em V1 do plugin, não temos esse sinal — pular este alerta se não houver plugin de marketing)

Narre o alerta no Passo 2. Não bloqueie.

## Princípios

- **Ordem fixa.** Mesmo que o autor pule um passo, narre que está pulando.
- **Um único ponto de aprovação.** Não pergunte issue por issue no passo 6.
- **Máximo 5 issues por semana** — sugestão forte, não bloqueio.
- **Decisão/premissa que aparecem viram entrada no DECISIONS.md** — não fica perdido na conversa.

## Exemplos de prompts que ativam

- "Vamos planejar a semana"
- "Planning de segunda-feira"
- "Review da semana passada e plano da próxima"

## Exemplos que NÃO ativam

- "Como tá o produto?" → progress-monitor
- "Cria a feature X" → create-feature
