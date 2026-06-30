---
name: capture-assumption
description: |
  Registra uma premissa (hipótese ainda não validada) no DECISIONS.md com
  tag [PREMISSA]. Use quando o autor explicitar uma hipótese — "estou
  assumindo", "vou partir do princípio", "premissa:", "se X for verdade",
  "estamos apostando que". Também use quando o autor disser que uma
  premissa foi validada ou refutada — neste caso, atualiza a entrada
  existente em vez de criar nova. NÃO use pra decisões já tomadas (use
  register-decision).
---

# capture-assumption

Append OU update no `DECISIONS.md` com tag `[PREMISSA]`. Premissas vivem no mesmo arquivo das decisões (decisão de design do plugin) com tag visível.

## Ativação

Triggers semânticos pra NOVA premissa:
- "estou assumindo X"
- "vou partir do princípio que X"
- "premissa: X"
- "se X for verdade"
- "estamos apostando que X"
- "minha hipótese é X"

Triggers pra ATUALIZAR premissa existente:
- "premissa X validou/se confirmou"
- "premissa X caiu/não funcionou/foi refutada"

NÃO ativar se:
- É uma decisão tomada → register-decision
- Não há cérebro Cervellone no contexto

## Procedimento

**Modo deferred (quando invocada dentro de weekly-planning ou outro orquestrador):** se o caller indicar 'compor sem committar', execute passos 1-5 (compor entrada e aprovar conteúdo) e PULE o git commit. Retorne o texto da entrada pro caller aplicar quando quiser.

## Procedimento — NOVA premissa

1. Confirme o cérebro (igual register-decision).
2. Detecte task ativa (igual register-decision).
3. Componha a entrada conforme formato `[PREMISSA]` em `_shared/cervellone-conventions.md`. Pergunte o que faltar:
   - Hipótese (no condicional)
   - Motivo de assumir
   - Alternativas se cair
   - Como validar (que sinal confirma/refuta)
4. Mostre formatada e peça aprovação.
5. Aplique append no topo do DECISIONS.md.

   ```bash
   git add DECISIONS.md
   git commit -m "assumption: <título curto>"
   ```

6. Se há task ativa: peça aprovação explícita ao autor antes de postar; após aprovação, use MCP `save_comment` com o texto: '📝 Premissa registrada no DECISIONS.md: **<título>** — <hipótese em 1 frase>. [Ver entrada completa](link relativo).'

## Procedimento — UPDATE premissa existente

1. **Localize a premissa** no DECISIONS.md. Grep por `[PREMISSA]` e identifique pelo título (peça desambiguação se houver várias).
2. **Update no lugar:**
   - Se VALIDADA: remova `[PREMISSA]` do título, adicione linha `**Validada em:** YYYY-MM-DD — <evidência>`. A entrada vira efetivamente uma decisão histórica. Use a data atual (rode `date +%Y-%m-%d` se precisar).
   - Se REFUTADA: mude pra `[PREMISSA] (REFUTADA)`, adicione `**Refutada em:** YYYY-MM-DD — <evidência> → ação tomada`. Se a ação tomada é nova decisão, sugira rodar register-decision em seguida. Use a data atual.
3. Aprovação antes do diff.
4. Commit:

   ```bash
   git add DECISIONS.md
   git commit -m "assumption: <título> — validada|refutada"
   ```

## Exemplos de prompts que ativam (nova)

- "Estou assumindo que CAC vai ficar abaixo de R$80 nessa categoria"
- "Premissa: Stripe Checkout é suficiente, não precisamos de Elements"
- "Vou partir do princípio que 80% dos usuários virão por mobile"

## Exemplos que ativam (update)

- "A premissa do CAC validou — rodamos 7 dias e está em R$62"
- "A premissa do Stripe Checkout caiu, precisa de Elements pra customizar campos"

## Exemplos que NÃO ativam

- "Decidi usar Stripe Checkout" → register-decision
- "Acho que CAC vai ficar baixo" → vago demais, peça pra explicitar premissa
