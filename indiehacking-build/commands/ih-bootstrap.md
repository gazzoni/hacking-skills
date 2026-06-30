---
description: Inicializa um cérebro novo de produto indie-hacker (clona template, cria repo remoto, prepara Linear)
---

Você está executando o comando `/ih-bootstrap`. Argumento recebido: $ARGUMENTS.

# Objetivo

Inicializar o cérebro de um produto indie-hacker novo (ou apontar pra um produto já existente em código). Sequência completa:

1. Validar pré-condições
2. Definir destino local
3. Clonar template público
4. Criar repo remoto novo
5. Refazer origin pro novo repo + primeiro push
6. Substituir placeholders pelo nome legível do produto
7. Criar CLAUDE.md local que aponta pro plugin
8. (Opcional) Inicializar project no Linear
9. Convidar pra rodar `brain-foundation` em seguida

# Princípios

- **Pontos de aprovação destrutivos:** passo 4 (cria repo remoto), passo 5 (push), passo 8 (Linear). Antes de cada um, mostre EXATAMENTE o que vai fazer e espere confirmação.
- **Tudo local antes do passo 4 é reversível com `rm -rf`** — pode rodar sem perguntar.
- **Slug é o argumento** ($ARGUMENTS deve conter um slug em kebab-case). Se não vier, peça.

# Passo 1: Validar pré-condições

Rode em paralelo:
- `gh auth status` (precisa estar autenticado)
- `git --version` (precisa existir)
- Verificar se MCP do Linear está conectado (tentar chamar `mcp__a1eaa395-...__list_teams` com limite 1 — falha graceful significa MCP indisponível, avise)

Se faltar `gh` ou `git`, pare e peça pro autor instalar.
Se MCP Linear faltar, prossiga avisando que passo 8 (Linear) vai pular.

# Passo 2: Definir destino

Pergunte:
> "Onde criar o cérebro? Default: `/Users/gazzoni/Documents/Projects/indie-hacking/<slug>/`. Outro caminho?"

Confirme o slug validando kebab-case (apenas a-z, 0-9, -). Se inválido, peça correção.

Pergunte também o nome legível do produto (ex: "Sight", "Licitacred") — usado pra substituir `<Nome do Produto>` nos placeholders.

# Passo 3: Clonar template público

URL fixa: `https://github.com/gazzoni/hacking-brain.git`

```bash
git clone https://github.com/gazzoni/hacking-brain.git <destino>
cd <destino>
rm -rf .git
git init
git branch -M main
```

Note: removemos o `.git` do template e re-inicializamos pra começar histórico limpo.

# Passo 4: Criar repo remoto novo (APROVAÇÃO)

Mostre ao autor:
> "Vou criar `gh repo create gazzoni/<slug>-cerebro --private`. Confirma? (sim/altera-nome/altera-visibilidade/cancela)"

Default privado. Se aprovar:

```bash
gh repo create gazzoni/<slug>-cerebro --private --description "Cérebro do produto <Nome>" --source=. --remote=origin
```

# Passo 5: Substituir placeholders

Em todos os arquivos do cérebro (BUSINESS.md, CONTEXT.md, README.md, etc.), troque `<Nome do Produto>` pelo nome legível fornecido no passo 2.

Use sed ou ferramenta de edição. Liste os arquivos modificados pro autor.

# Passo 6: Primeiro push (APROVAÇÃO)

Antes do push, certifique-se que o passo 5 (placeholders) foi feito e adicione tudo + commit local.

```bash
git add .
git commit -m "chore: foundation do cérebro a partir do template Cervellone"
```

Mostre ao autor o `git log` e confirme:
> "Vou fazer push pro origin. Confirma?"

Se sim:

```bash
git push -u origin main
```

# Passo 7: Criar CLAUDE.md local

Arquivo `<destino>/CLAUDE.md` com conteúdo:

```markdown
# CLAUDE.md — Cérebro de <Nome>

Este é um cérebro Cervellone. O plugin `indiehacking-build` opera ele.

## Princípio fundamental
Git é a fonte de verdade da execução. Linear é projeção mantida pelo agente.
Quando divergem, git ganha.

## Skills disponíveis (autoacionadas via triggers)
- `brain-foundation` — popula o cérebro em sessão de planning
- `register-decision` — append no DECISIONS.md
- `capture-assumption` — DECISIONS.md com tag [PREMISSA]
- `create-feature` — gera Artefato Mínimo em features/
- `linearize-feature` — Artefato → issues no Linear
- `progress-monitor` — leitura passiva de progresso
- `weekly-planning` — sessão semanal de priorização
- `task-execution` — modo execução de task (chat = task via branch git)

## Convenções
- Estrutura de arquivos do template-master Cervellone (ver `_system/conceitos.md`)
- Linear H4NDS: Done = produção validada. Branch nasce da issue.
- Toda ação destrutiva (Linear, push) propõe → aprovação → executa.
```

# Passo 8: Inicializar Linear project (APROVAÇÃO + OPCIONAL)

Pergunte:
> "Inicializar project no Linear agora? (sim/depois)"

Se sim, e MCP Linear disponível:
1. Listar teams via MCP; localizar o time "Engenharia" (que já existe no workspace). Se houver convenção de sub-time por produto (verificar via listar sub-times de Engenharia), criar sub-time `<Nome> Engenharia` se ainda não existir. Pergunte ao autor antes de criar.
2. Listar templates de project; pegar "Produto zero-to-one"
3. Mostrar ao autor o que vai criar: nome do project, time, milestones (M0 Fundação + M1..Mn placeholders + Lançamento em produção), issues core do M0
4. Se confirmar, criar via MCP

# Passo 9: Convite pra brain-foundation

Última mensagem:
> "Cérebro de <Nome> inicializado em <destino>. Próximo passo natural: rodar `brain-foundation` pra popular BUSINESS/CONTEXT/ARCHITECTURE em conversa. Vamos agora?"

# Edge cases

- Destino já existe e não está vazio: pare, pergunte se sobrescreve
- `gh repo create` falha porque o nome já existe: ofereça `<slug>-cerebro-2` ou pergunte novo nome
- MCP Linear retorna erro: avise e ofereça pular passo 8
