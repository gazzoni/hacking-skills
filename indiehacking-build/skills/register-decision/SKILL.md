---
name: register-decision
description: |
  Registra uma decisão de rota no DECISIONS.md do cérebro Cervellone.
  Use quando o autor expressar uma decisão tomada — frases como "decidi X",
  "vou usar X", "escolhi X em vez de Y", "vamos com X", "optei por X".
  NÃO use pra premissas/hipóteses (use capture-assumption).
  NÃO use pra anotações soltas; uma decisão tem motivo e alternativas.
---

# register-decision

Append append-only no `DECISIONS.md` com formato canônico Cervellone.

## Ativação

Triggers semânticos:
- "decidi/decidimos X"
- "vou usar/usaremos X"
- "escolhi X em vez de Y"
- "vamos com X"
- "optei por X"
- "ficou definido que X"

NÃO ativar se:
- Frase tem "estou assumindo", "premissa", "se X for verdade", "vou partir do princípio" → capture-assumption
- Não há cérebro Cervellone no contexto (sem `_system/agente.md` no cwd ou ancestrais)

## Procedimento

**Modo deferred (quando invocada dentro de weekly-planning ou outro orquestrador):** se o caller indicar 'compor sem committar', execute passos 1-5 (compor entrada e aprovar conteúdo) e PULE o git commit. Retorne o texto da entrada pro caller aplicar quando quiser.

### Passos ordinários (online commit)

1. **Confirme o cérebro:** rode `git rev-parse --show-toplevel` e verifique se há `_system/agente.md` no diretório raiz do repo atual. Se não houver, avise e pare.

2. **Detecte se há task ativa:** rode `git branch --show-current`. Se o nome da branch contém ID Linear (padrão regex `^[a-z]+-\d+`), guarde o ID — vamos espelhar a decisão como comentário na issue.

3. **Componha a entrada** seguindo o formato em `_shared/cervellone-conventions.md`. Pergunte ao autor o que faltar:
   - Decisão (1 frase)
   - Motivo (por que essa)
   - Alternativas consideradas
   - Consequências (o que muda)

4. **Mostre a entrada formatada e peça aprovação:**

   > "Vou adicionar isso no topo do DECISIONS.md. OK?
   > ```
   > ## YYYY-MM-DD — <título>
   > **Decisão:** ...
   > **Motivo:** ...
   > **Alternativas consideradas:** ...
   > **Consequências:** ...
   > ```"
   
   Use a data atual (rode `date +%Y-%m-%d` se precisar).

5. **Se a decisão mexe num fluxo:** pergunte se o Mermaid no UX.md também precisa atualizar. Se sim, mostre o diff do Mermaid junto.

6. **Aplique:** após aprovação, faça append no topo da seção de decisões do DECISIONS.md (depois do cabeçalho do arquivo, antes da entrada mais recente). Commit local:

   ```bash
   git add DECISIONS.md UX.md  # UX.md só se mudou
   git commit -m "decision: <título curto>"
   ```

7. **Se há task ativa (passo 2 detectou ID Linear):** poste comentário na issue:

   > "Decisão registrada no DECISIONS.md desta task: [link pro arquivo no repo]
   > **<título>**: <decisão em 1 frase>"

   Via MCP Linear, ferramenta `save_comment`. Aprovação explícita antes.

## Exemplos de prompts que ativam

- "Decidi usar Supabase pra auth"
- "Vamos com Stripe Checkout em vez de Stripe Elements"
- "Optei por monorepo, vai ficar mais fácil"

## Exemplos que NÃO ativam

- "Estou pensando em usar Supabase" → conversa, sem decisão
- "Se a gente usasse Supabase..." → especulação
- "Vou assumir que o usuário tem Google" → capture-assumption
