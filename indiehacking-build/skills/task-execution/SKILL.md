---
name: task-execution
description: |
  Skill guardiã do ciclo de vida de uma task Linear. Ativa QUALQUER momento
  que o autor mencione uma task por ID ("faça GAZ-03", "vou trabalhar na
  LIV-635", "vamos na task X") — ou implicitamente quando há branch git
  ativa com ID Linear num cérebro Cervellone. A partir daí, mantém o
  Linear alinhado com o que acontece no chat: puxa contexto, muda status,
  registra decisões como comentários, cria sub-issues, valida DoD, e cobra
  as transições que a automação git→status não faz.
---

# task-execution

Skill "ambiente" que assume o controle do ciclo de vida da issue Linear a partir do momento em que o autor mencionar uma task.

## Ativação

**Explícita (mais comum):** o autor menciona uma task por ID ou por comando de execução.
- "Faça a task LIV-635"
- "Vou trabalhar na GAZ-03"
- "Começa a issue LIV-127"
- "Vamos na LIV-635"
- "Estou na task X"
- Qualquer variação com ID Linear (regex: `[A-Z]+-\d+`) + intenção de executar

**Implícita:** cwd (ou ancestrais) contém `_system/agente.md` E há branch git com ID Linear no nome (regex `^[a-z]+-\d+`). A skill embarca silenciosamente.

**Suprimir ativação implícita quando:** `brain-foundation`, `create-feature` ou `weekly-planning` estão ativas como skill primária. Nesses casos é planejamento, não execução.

## Procedimento

### 1. Puxar contexto da task

Assim que ativar, chame Linear MCP `get_issue` com o ID e leia:
- Título
- Tipo (Feature/Bug/Spike/Discovery/Chore/Improvement)
- Descrição completa (inclui Artefato Mínimo, DoD, Notas)
- Status atual
- Milestone
- Prioridade

Mostre 1 linha no topo:
> 📍 **LIV-635 · Add Stripe webhook · Feature · M2 · status: Todo**
> DoD: webhook recebe evento em produção e marca order como paid

Se o ID mencionado não existir no Linear, avise e pare — não invente.

### 2. Mudar status pra In Progress imediatamente

Se `status atual != In Progress` e a task está sendo iniciada agora, chame Linear MCP `save_issue` com status `In Progress`. Um único pedido de aprovação leve:

> "Status atual: Todo. Movendo pra In Progress. OK?"

Aprovação = "sim" ou o autor seguir com a execução sem contestar. Registre a transição.

Este passo resolve o gap da automação Linear H4NDS, que só dispara `→ In Progress` quando a branch é copiada pela UI do Linear ou aberta via coding-tool integration — muitas tasks (Discovery/Spike/Chore-doc, ou início por terminal) nunca acionam esse gatilho.

### 3. Executar a task

O que "executar" significa depende do tipo da task, extraído no Passo 1:

**Feature / Bug (código):**
- Precisa de branch git com o ID Linear. Se ainda não há branch, avise que a UI do Linear é o lugar correto pra copiar a branch (dispara também a integração do editor). Ofereça criar via terminal só se o autor pedir explicitamente.
- Implemente código conforme conversa
- Registre decisões que aparecerem em DECISIONS.md via `register-decision`, com espelho na issue (passo 5 abaixo)
- Ao pedir commit, siga o Passo 4
- Ao pedir PR, siga o Passo 6

**Spike (investigação técnica com timebox):**
- Trabalho é conversa e possivelmente prova-de-conceito descartável
- Output esperado (do template Spike) = decisão, recomendação, POC, estimativa
- Registre a resposta no DECISIONS.md ou onde a issue apontar
- Sem PR, sem código de produção

**Discovery (investigação de produto/domínio):**
- Trabalho é conversa e leitura de fontes
- Output esperado = mapa, modelo, definição — vira input de Artefato Mínimo
- Registre o output no cérebro (features/, DECISIONS.md, ou UX.md conforme apropriado)
- Sem PR

**Chore (infra/CI/refactor sem mudança de comportamento):**
- Pode ter código (branch + PR) ou não (config em SaaS externa)
- Se tem código, segue igual Feature
- Se não, comportamento é como Spike sem output textual

### 4. Formatar commits (quando há código)

Ao autor pedir commit, proponha mensagem:

```
<tipo>: <descrição curta>

<corpo opcional>

Ref: LIV-635
```

Tipo: `feat`/`fix`/`chore`/`refactor`/`test`/`docs`. Mostre a mensagem, espere OK, execute. Não push automático.

### 5. Espelhar decisões e premissas na issue

Sempre que `register-decision` ou `capture-assumption` ativar dentro deste contexto (task ativa detectada), adicione comentário na issue Linear via MCP `save_comment`:

> "📝 Decisão registrada no DECISIONS.md: **<título>** — <decisão em 1 frase>. [Ver entrada completa](link)"

Peça aprovação explícita antes de postar o comentário.

### 6. Formatar PR (quando há código)

Ao autor pedir PR:

**Título** = título exato da issue (regra Git attachment format Title+Repository do setup).

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

Target: `develop` por default. Mostre preview, peça OK, execute `gh pr create --base develop --title "<título>" --body-file <file>`.

### 7. Vigilância de transições esperadas

Depois de propor PR, mentalmente registre o estado esperado. Nos turnos seguintes, se o autor mencionar palavras-chave de transição, chame `get_issue` e compare:

| Autor diz | Estado esperado | Se divergente |
|---|---|---|
| "PR aberto" / "PR pra develop" | In Review | cobrar transição |
| "mergeei" / "PR mergeado em develop" | Staging | cobrar transição |
| "validei em develop" | Staging (segue lá até merge em main) | avisar próximo passo é PR pra main |
| "PR pra main" / "mergeei em main" | Done (se deploy automático) | verificar. Se não moveu, deploy é manual → cobrar Done manual após confirmação de prod |
| "deployei em prod" / "está em prod" | Done | cobrar Done se não moveu |

Regra: **nunca assuma que a automação git→status fez sozinha**. Sempre verifique via `get_issue` antes de aceitar que o status está certo.

### 8. Sub-issues durante execução

Mantenha lista interna `sub_issues = []`. Ao aparecer:
- "ah, também precisa fazer X"
- "vou precisar criar uma migration"
- "encontrei um bug em outro lugar"

Acumule. Em momentos naturais (antes do PR, no fim do chat), proponha em lote:

> "Surgiram 3 sub-issues e 1 bug durante essa task. Crio agora?
> 1. [Chore] Migration pra idempotency_key
> 2. [Chore] README com setup do webhook local
> 3. [Feature] Painel de dead-letter queue
> 4. [Bug] Email @hotmail não chega (não relacionado)"

Aprovação em lote → criar via MCP `save_issue` direto (não delegue pra linearize-feature). Sub-issues 1-3 viram filhas de LIV-635; bug 4 vai solto com label `Bug`.

### 9. Fechamento — cobrar Done

Ao autor dizer "acabei", "task pronta", "vamos pra próxima", ou equivalente:

1. Chame `get_issue`
2. Compare status atual com estado esperado dado o tipo:

| Tipo | Fluxo esperado até Done |
|---|---|
| Feature/Bug (código) | Todo → In Progress → In Review → Staging → Done. Cada transição deve ter acontecido. |
| Spike | Todo → In Progress → Done (direto, sem staging). Output ficou registrado onde? |
| Discovery | Todo → In Progress → Done. Output (mapa/definição) ficou registrado? |
| Chore-doc | Todo → In Progress → Done. |
| Chore-código | Igual Feature |

3. Se DoD tem checklist, valide cada item. Não silencie ausências:
> "DoD:
> ✓ Webhook recebe evento em produção (confirmado pelo autor)
> ✗ Erro 4xx/5xx logged (o autor mencionou skipping — confirma?)"

4. Se o status esperado é Done mas está travado (ex: em Staging depois de merge em main, quando devia ter fechado): cobre transição manual via `save_issue`. Aprovação explícita.

5. Se DoD incompleto mas autor quer fechar mesmo assim: registra a decisão de fechar (com nota) e move.

## Sempre

- Confira o status via `get_issue` a cada checkpoint. Nunca assuma.
- Uma task ativa por chat. Se o autor pular pra outra, encerre limpo o contexto anterior (cobre Done ou status esperado atual) antes de mudar.
- Toda escrita no Linear (`save_issue`, `save_comment`) passa por aprovação explícita.
- Decisões/premissas viajam automaticamente pra issue (via aprovação já dada no register-decision/capture-assumption).

## O que NÃO faz

- Não merge sozinha
- Não push sem pedido
- Não cria branch por conta própria (setup H4NDS força criar pela UI do Linear pra branch ter ID que dispara automações do editor)

## Exemplos que ativam

- "Faça a task LIV-635"
- "Vou trabalhar na GAZ-03"
- "Começa a discovery LIV-127"
- "Vamos executar a LIV-635"
- Checkout numa branch `liv-635-add-stripe-webhook` dentro de um cérebro

## Exemplos que NÃO ativam

- "Cria uma issue pra X" → linearize-feature ou criação direta
- "Como tá o milestone?" → progress-monitor
- "Planejar a semana" → weekly-planning
