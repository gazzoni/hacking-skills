# Convenções Cérebro Cervellone

> Referenciado pelas skills quando elas interagem com arquivos do cérebro.

## Estrutura
- `README.md` — guia
- `BUSINESS.md` — problema, cliente, modelo de negócio
- `CONTEXT.md` — visão, stack, ambientes, repos, definição de V1 (lido primeiro)
- `ARCHITECTURE.md` — arquitetura, data model, restrições
- `REQUIREMENTS.md` — RF e RNF
- `DECISIONS.md` — log append-only de decisões (e premissas com tag [PREMISSA])
- `UX.md` — experiência + fluxos Mermaid
- `DESIGN.md` — decisões de design + links
- `features/` — um Artefato Mínimo por feature (template: `features/_template-artefato.md`)
- `_system/agente.md` — como o agente opera (igual entre produtos)
- `_system/conceitos.md` — definições (igual entre produtos)
- `CLAUDE.md` — gerado pelo /ih-bootstrap, aponta pro plugin

## Princípios
1. Atualizar = decidir. Decisão de rota → 5 linhas no DECISIONS.md.
2. Tudo destilado: cada arquivo se lê em poucos minutos.
3. Cada coisa na ferramenta certa (fluxo visual → Figma; business design → Studio).
4. Tem dono.

## Formato de entrada no DECISIONS.md
```
## YYYY-MM-DD — Título curto da decisão
**Decisão:** <uma frase>
**Motivo:** <por que essa decisão>
**Alternativas consideradas:** <quais foram pesadas>
**Consequências:** <o que muda no produto/arquitetura/escopo>
```

## Formato de entrada [PREMISSA]
```
## YYYY-MM-DD — [PREMISSA] Título da hipótese
**Hipótese:** <uma frase no condicional>
**Motivo de assumir:** <por que estamos partindo daqui>
**Alternativas se cair:** <plano B>
**Como validar:** <que sinal/evento confirma ou refuta>
```

Quando validada:
- Remover `[PREMISSA]`
- Adicionar `**Validada em:** YYYY-MM-DD — <evidência>`

Quando refutada:
- Manter `[PREMISSA] (REFUTADA)`
- Adicionar `**Refutada em:** YYYY-MM-DD — <evidência> → ação tomada`

## Formato de Artefato Mínimo de Contexto
Ver `features/_template-artefato.md`. Campos:
- O que é (1 frase)
- Por que existe (JTBD + link RF do REQUIREMENTS.md)
- Referência visual/lógica (link Figma ou descrição)
- O que NÃO é (limites)
- Definition of Done (verificável em ambiente acordado)
- Milestone associado
- Notas pro agente (opcional)
