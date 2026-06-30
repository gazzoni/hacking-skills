---
name: create-feature
description: |
  Gera um Artefato Mínimo de Contexto em features/ a partir de um
  requisito do REQUIREMENTS.md. Use quando o autor pedir pra criar/modelar
  uma feature — "vamos criar a feature X", "novo artefato pra X", "preciso
  modelar X em features/". NÃO use pra criar issues no Linear (use
  linearize-feature em seguida).
---

# create-feature

Cria `features/<slug>.md` seguindo `features/_template-artefato.md`.

## Ativação

Triggers:
- "vamos criar a feature X"
- "novo artefato pra X"
- "preciso modelar X em features"
- "criar feature X"

NÃO ativar se:
- Pedido é pra criar issues no Linear → linearize-feature
- Não há cérebro Cervellone no contexto

## Procedimento

1. **Confirme o cérebro** e exista `features/_template-artefato.md`. Se não houver, pare.

2. **Pergunte o nome da feature** (legível) e derive um slug em kebab-case.

3. **Linka ao requisito.** Leia o REQUIREMENTS.md e pergunte:
   > "Qual RF do REQUIREMENTS.md essa feature implementa? [lista os RFs detectados no arquivo]. Se nenhum cabe, vamos criar o RF primeiro."
   
   Se o RF não existe, peça o autor pra escrever 1 frase de RF antes de continuar. Adicione o RF ao REQUIREMENTS.md como parte do mesmo commit.

4. **Pergunte as cinco coisas do template** (uma por uma, não tudo de vez):
   - **O que é** (1 frase, linguagem de produto)
   - **Por que existe** (JTBD/dor que resolve, ligado ao RF)
   - **Referência visual/lógica** (link Figma ou descrição do comportamento)
   - **O que NÃO é** (limites — anti scope creep)
   - **Definition of Done** (verificável, em ambiente acordado)

5. **Pergunte o milestone associado.** Leia do Linear via MCP (`list_milestones` filtrado pelo project ativo, se houver `CONTEXT.md` apontando pra ele). Mostre as opções e peça escolha.

6. **Notas pro agente (opcional).** Pergunte se há decisões do DECISIONS.md ou restrições do ARCHITECTURE.md que tocam essa feature.

7. **Mostre o arquivo completo** e peça aprovação.

8. **Crie o arquivo** em `features/<slug>.md` e commit:

   ```bash
   git add features/<slug>.md REQUIREMENTS.md  # REQUIREMENTS só se mudou
   git commit -m "feat: artefato mínimo — <nome>"
   ```

9. **Convide pra próxima skill:**
   > "Artefato criado. Próximo passo: rodar `linearize-feature` pra quebrar em issues no Linear. Vamos?"

## Exemplos de prompts que ativam

- "Vamos criar a feature de checkout com Stripe"
- "Preciso modelar o onboarding em features"
- "Novo artefato pra envio de email transacional"

## Exemplos que NÃO ativam

- "Cria as issues no Linear pra isso" → linearize-feature
- "Decidi usar Stripe pra checkout" → register-decision
