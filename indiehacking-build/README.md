# indiehacking-build

Plugin Claude Code que apoia a fase Build (Gate 2) da metodologia indie-hacker
descrita em [este artigo](https://medium.com/@gazzonigui/indie-hacker-voc%C3%AA-sabe-explorar-o-potencial-dos-seus-projetos-16ba7473fe9e).

Cobre tudo do Gate 2: do Product Brief pronto até o handoff pra Graduação.

## Instalação (durante desenvolvimento)

```bash
claude plugin marketplace add /Users/gazzoni/.claude/marketplaces/indiehacking
claude plugin install indiehacking-build@indiehacking
```

## Skills disponíveis

| Skill | Quando ativa |
|---|---|
| `brain-foundation` | "vamos modelar o produto", "preencher o cérebro" |
| `register-decision` | "decidi X", "vamos com X" |
| `capture-assumption` | "estou assumindo X", "premissa: X" |
| `create-feature` | "vamos criar a feature X" |
| `linearize-feature` | "cria as issues dessa feature" |
| `progress-monitor` | "como tá o produto?" |
| `weekly-planning` | "vamos planejar a semana" |
| `task-execution` | implícita: dentro de cérebro Cervellone com tool use em código/git |

## Comandos

| Comando | O que faz |
|---|---|
| `/ih-bootstrap <slug>` | Cria cérebro novo de produto (clona template, repo remoto, Linear opcional) |

## Dependências

- Plugin Linear MCP instalado e autenticado
- `gh` CLI autenticado
- `git`
- Cérebro Cervellone (template em [gazzoni/hacking-brain](https://github.com/gazzoni/hacking-brain))

## Pré-condições por produto

O plugin assume:
- Cada produto tem repo separado de cérebro (a documentação NÃO mora no repo de código)
- Linear configurado conforme [convenções H4NDS](./_shared/linear-conventions.md)
- Branch nasce sempre da issue (com ID) na UI do Linear

## Versão

v0.1.0 — V1 do plugin. Próximos: `indiehacking-model` (Gate 1: Modelagem) e `indiehacking-marketing` (Gate 2 canal + Gate 3 Graduação).
