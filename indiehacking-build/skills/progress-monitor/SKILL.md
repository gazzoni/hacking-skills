---
name: progress-monitor
description: |
  Leitura passiva do progresso do projeto cruzando Linear + DECISIONS.md.
  Use quando o autor perguntar "como tá o milestone X", "% de progresso",
  "estamos no plano?", "tem desvio?", "dá uma olhada no projeto". NÃO
  escreve nada — só narra. Pra sessão ativa de planejamento use
  weekly-planning.
---

# progress-monitor

Foto do estado do projeto, com explicação de desvio via DECISIONS.md.

## Ativação

Triggers:
- "como tá o milestone X"
- "% de progresso"
- "estamos no plano"
- "tem desvio"
- "olhada rápida no projeto"
- "status do produto"

## Procedimento

1. **Identifique o project ativo.** Leia `CONTEXT.md` se houver referência; caso contrário liste projects do Linear via MCP e pergunte qual.

2. **Pegue dados do Linear:**
   - Issues do project agrupadas por milestone
   - Status atual de cada issue
   - Datas das milestones (se preenchidas)

3. **Calcule métricas por milestone:**
   - Total de issues
   - Done (somente categoria Completed — Staging NÃO conta)
   - In Progress + In Review + Staging (em andamento)
   - Todo + Backlog (não começado)
   - % completude = Done / Total

4. **Compare planejado vs realizado:**
   - Se milestone tem data target: calcule dias restantes vs ritmo de conclusão (issues Done por dia médio)
   - Sinalize "no plano" / "atrasado X dias" / "adiantado"

5. **Busque narrativa no DECISIONS.md:**
   - Leia decisões dos últimos 14 dias
   - Se houver desvio, tente correlacionar (decisão de 10/06 = mudou stack → atraso em M1 explicado)

6. **Saída em texto (não escreve em arquivo):**

   > **Foto rápida — produto <nome>**
   >
   > | Milestone | Done | Andamento | Backlog | % | Status |
   > |---|---|---|---|---|---|
   > | M0 Fundação | 7 | 0 | 0 | 100% | ✅ |
   > | M1 Onboarding | 3 | 2 | 1 | 50% | atrasado 4d |
   > | M2 Checkout | 0 | 1 | 4 | 0% | no plano |
   > | Lançamento | 0 | 0 | 1 | 0% | depende de M2 |
   >
   > **Desvio em M1:** atraso correlaciona com decisão de 10/06 (trocar Auth.js → Supabase Auth) que adicionou 3 dias de retrabalho. Documentado no DECISIONS.md.

## Princípios

- **Não escreve nada.** Não cria/atualiza issue, não mexe em DECISIONS.md.
- **Foto curta.** Tabela + 1-2 parágrafos. Não vire relatório de 5 páginas.
- **Toda métrica explicada por evidência** — % é fato; "no plano" é interpretação que precisa do DECISIONS.md.

## Exemplos de prompts que ativam

- "Como tá o produto?"
- "Olhada rápida no projeto"
- "Estamos no plano?"
- "% de M2"

## Exemplos que NÃO ativam

- "Vamos planejar a semana" → weekly-planning
- "Cria a feature X" → create-feature
