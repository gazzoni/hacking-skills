# hacking-skills

Pack de plugins Claude Code que apoia a metodologia indie-hacker do Gazzoni (descrita em [este artigo](https://medium.com/@gazzonigui/indie-hacker-voc%C3%AA-sabe-explorar-o-potencial-dos-seus-projetos-16ba7473fe9e)).

## Plugins

- **indiehacking-build** — Fase Build (Gate 2). Disponível em v0.1.0.
- **indiehacking-model** — Fase Modelagem (Gate 1). Futuro.
- **indiehacking-marketing** — Fase Marketing (Gate 2 canal + Gate 3 Graduação). Futuro.

Cada plugin vive numa subpasta deste monorepo.

## Instalação

```bash
claude plugin marketplace add gazzoni/hacking-skills
claude plugin install indiehacking-build@indiehacking
```

## Estrutura

```
hacking-skills/
├── .claude-plugin/
│   └── marketplace.json
├── indiehacking-build/      # plugin v0.1.0
└── (futuros plugins entram como subpastas)
```

## Pré-requisitos por plugin

Ver o `README.md` dentro de cada subpasta do plugin.
