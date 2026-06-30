---
name: linearize-feature
description: |
  Lê um Artefato Mínimo de Contexto em features/<slug>.md e propõe N
  issues fatiadas vertical no Linear (com templates Feature/Bug/Spike/
  Discovery do setup H4NDS), com aprovação em lote. Use quando o autor
  pedir pra "criar issues dessa feature", "linearizar X", "quebrar X em
  tasks". Ativa também ao final de create-feature.
---

# linearize-feature

Artefato Mínimo → issues no Linear, com aprovação em lote.

## Ativação

Triggers:
- "criar issues dessa feature"
- "linearizar a feature X"
- "quebrar a feature X em tasks"
- "criar as tasks dessa feature no Linear"
- continuação natural após `create-feature`

## Procedimento

1. **Identifique o artefato.** Liste `features/*.md` (exclui `_template-artefato.md`). Se houver vários, pergunte qual. Se contexto recente menciona um (após create-feature), use direto.

2. **Leia o artefato completo.** Extraia: o que é, DoD, milestone associado, RF linkado, notas técnicas.

3. **Leia Linear via MCP:**
   - Time de Engenharia + sub-time do produto (do CONTEXT.md ou via lista)
   - Project ativo
   - Milestones existentes (pra mapear o milestone do artefato)

4. **Proponha as issues.** Fatie vertical (regra `_shared/linear-conventions.md`): entrega ponta-a-ponta pequena, task > 2-3 dias quebra. Para cada issue:
   - Tipo (Feature/Bug/Spike/Discovery/Chore/Improvement)
   - Título (curto, imperativo)
   - Descrição usando o template Linear apropriado, preenchido a partir do artefato:
     - Feature: Artefato Mínimo (link), Escopo (do artefato), DoD, Fora de escopo, Notas técnicas
     - Spike: Pergunta a responder, Timebox (peça ao autor), Output esperado, Critério de conclusão
     - Discovery: idem Spike adaptado
   - Labels: Tipo (G), Tech (L) (Frontend/Backend/Infra/Mobile/Data — inferir do conteúdo)
   - Prioridade nativa (pergunte; default No priority)
   - Milestone (o do artefato)

5. **Mostre a lista numerada ao autor:**

   > "Vou criar estas issues. Confirma o lote completo, ou ajusta antes?
   > 1. [Feature · M2 · High] Setup do Stripe Checkout endpoint
   > 2. [Feature · M2 · Medium] Página de pagamento (frontend)
   > 3. [Feature · M2 · Medium] Webhook Stripe → atualiza order
   > 4. [Spike · M2 · No priority · 1 dia] Investigar handling de chargebacks
   > 5. [Chore · M2 · No priority] Logging/observabilidade do flow"

6. **Espere aprovação única.** Se autor editar (mudar ordem, remover, ajustar prioridade), refazer e mostrar de novo.

7. **Crie via MCP em sequência.** Para cada issue:
   - Chamar `save_issue` com os campos compostos
   - Capturar o ID retornado
   - Continuar

8. **Reportar resultado:**
   > "Criadas 5/5 issues. IDs: LIV-127, LIV-128, LIV-129, LIV-130, LIV-131. Todas linkadas ao milestone M2 e ao artefato features/checkout-stripe.md."

   Se alguma falhou, mostre qual e o erro. NÃO desfaça as que entraram (Linear lida bem com recriar item faltante).

## Princípios

- **Um único ponto de aprovação** — não pergunte issue por issue
- **Link de volta pro cérebro** — toda issue tem ref ao arquivo do artefato
- **Sem prioridade alta sem motivo** — default é No priority
- **Não silenciar erro** — se algo falhou, autor precisa saber

## Exemplos de prompts que ativam

- "Cria as issues dessa feature de checkout"
- "Lineariza a feature de onboarding"
- "Quebra essa feature em tasks no Linear"

## Exemplos que NÃO ativam

- "Cria a feature de checkout" → create-feature
- "Como tá o milestone M2?" → progress-monitor
