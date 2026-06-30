---
name: task-execution
description: |
  Modo "chat = task": detecta a issue Linear pelo nome da branch git
  ativa, mantém contexto da task durante o chat, formata commits e PRs no
  padrão do setup, cria sub-issues que aparecem durante a execução,
  espelha decisões/premissas como comentário na issue, checa DoD antes
  do PR. Ativa AUTOMATICAMENTE quando há tool use de código/git num
  diretório com _system/agente.md (cérebro Cervellone presente).
---

# task-execution

Skill "ambiente" que orienta toda a execução de uma task.

## Ativação

**Implícita (preferida):** logo no início da sessão, se o cwd ou ancestrais contêm `_system/agente.md`, esta skill é o pano de fundo. Não precisa de prompt explícito.

**Explícita:** "estou na task X", "vou começar essa issue".

## Procedimento

### 1. Detectar task atual

Logo no início:
```bash
git branch --show-current
```

Extraia ID Linear do nome da branch via regex `^[a-z]+-\d+`. Padrão típico: `liv-635-add-stripe-webhook`.

**Se há ID:** chame Linear MCP `get_issue` com o ID. Pegue título, descrição, tipo, milestone, DoD.

Mostre 1 linha no topo da sessão:

> 📍 **LIV-635 · Add Stripe webhook · Feature · M2**
> DoD: webhook recebe evento em produção e marca order como paid

**Se não há ID (branch livre):** avise:
> "Branch atual sem ID Linear. Pra conectar ao board, crie a issue no Linear e copie a branch de lá. Quer que eu crie a issue agora?"

Se autor topar, crie a issue diretamente via MCP `save_issue` com os campos básicos: título (peça ao autor), tipo (peça: Feature/Bug/Spike/Discovery/Chore/Improvement), e o time correto. Não delegue pra linearize-feature — essa skill exige um Artefato Mínimo de Contexto.

### 2. Manter contexto durante o chat

Se o autor desviar a conversa, responda, mas no fim lembre:
> "Voltando — você estava em LIV-635."

### 3. Formatar commits

Quando autor pedir commit, proponha mensagem:

```
<tipo>: <descrição curta>

<corpo opcional>

Ref: LIV-635
```

Tipo (do conventional commits style): `feat`/`fix`/`chore`/`refactor`/`test`/`docs`.

Mostre a mensagem completa, espere OK. Rode `git commit -m "..."`. NÃO push automático.

### 4. Formatar PRs

Quando autor pedir PR:

**Título:** = título exato da issue (regra do Git attachment format Title+Repository do setup).

**Body:**
```markdown
## Issue
- LIV-635 · Add Stripe webhook
- Artefato: [features/checkout-stripe.md](link)

## DoD (da issue)
- [ ] Webhook recebe evento em produção
- [ ] Order marca como paid corretamente
- [ ] Erro 4xx/5xx logged

## Decisões tomadas nesta task
- YYYY-MM-DD — Usar idempotency-key do Stripe (DECISIONS.md#...)

## Notas
<o que o autor quiser>
```

Target: `develop` por default (setup H4NDS).

Mostre preview, peça OK, rode:
```bash
gh pr create --base develop --title "<título>" --body-file <file>
```

### 5. Criar sub-issues durante execução

Mantenha lista interna `sub_issues = []`. Triggers naturais:
- "ah, também precisa fazer X"
- "vou precisar criar uma migration"
- "encontrei um bug em outro lugar"

Acumule. Em momentos naturais (fim de rodada de implementação, antes do PR, no fim do chat), proponha:

> "Surgiram 3 sub-issues e 1 bug durante essa task. Crio agora?
> 1. [Chore] Migration pra adicionar idempotency_key na tabela orders
> 2. [Chore] Atualizar README com setup do webhook local (Stripe CLI)
> 3. [Feature] Painel de dead-letter queue pra eventos com falha
> 4. [Bug] Email de confirmação não chega quando domain @hotmail (não relacionado)"

Aprovação em lote. Crie via MCP `save_issue` direto — não use linearize-feature (que requer artefato). Sub-issues 1-3 viram filhas de LIV-635; bug 4 vai solto com label `Bug`.

### 6. Espelhar decisões/premissas na issue

Sempre que `register-decision` ou `capture-assumption` ativar dentro deste contexto:
- Adicione comentário na issue Linear:
  > "📝 Decisão registrada no DECISIONS.md: **<título>** — <decisão em 1 frase>. [Ver entrada completa](link)"

Use MCP `save_comment`. Aprovação implícita (já houve aprovação da decisão em si).

### 7. Checar DoD antes do PR final

Antes de propor `gh pr create`, mostre a checklist de DoD da issue e pergunte cada item:
> "Vamos pelo DoD da issue:
> [ ] Webhook recebe evento em produção — ainda não? OK, esse vai em staging primeiro.
> [ ] Order marca como paid — testou local?
> [ ] Erro 4xx/5xx logged — adicionou?"

Se faltar item, avise mas deixe seguir (autor decide se manda PR incompleto). Não silencia ausência.

## O que NÃO faz

- Não merge sozinha
- Não push sem pedido
- Não fecha issue (automação git→status do setup cuida quando merge em main acontece)
- Não cria branch (setup força criar pela UI do Linear pra branch ter ID)

## Princípios

- **Sempre 1 task ativa por chat.** Se autor pular pra outra branch no meio, avise o contexto mudou.
- **DoD é cordão umbilical da issue.** Sempre cruze antes de fechar.
- **Nada destrutivo sem aprovação** (push, PR, criar issue).
- **Decisões viajam pra issue automaticamente.** Mas a decisão original aprovou (autor não precisa aprovar de novo cada comentário).

## Exemplos de prompts que ativam (explicitamente)

- "Vou começar essa issue LIV-635"
- "Estou na task X"

## Ativação implícita

Qualquer pedido de tool use em código/git num diretório com `_system/agente.md`. Skill embarca silenciosamente e mostra a linha 📍 no início.

**Suprimir ativação implícita quando:** uma das skills `brain-foundation`, `create-feature`, ou `weekly-planning` está ativa como skill primária no turno atual. Esses casos são sessões de planejamento/modelagem, não de execução de task — a linha 📍 ficaria como ruído.
