---
name: brain-foundation
description: |
  Popula o cérebro Cervellone em sessão de planning — preenche BUSINESS,
  CONTEXT, ARCHITECTURE, REQUIREMENTS, UX, DESIGN em conversa. Detecta
  automaticamente modo greenfield (cérebro vazio) vs alimentação
  retroativa (já há código de produto). Use quando o autor falar "vamos
  modelar o produto", "preencher o cérebro", "definir
  BUSINESS/CONTEXT/ARCHITECTURE", ou no fim do /ih-bootstrap.
---

# brain-foundation

Sessão de planning que popula os arquivos centrais do cérebro.

## Ativação

Triggers:
- "vamos modelar o produto"
- "preencher o cérebro"
- "definir BUSINESS / CONTEXT / ARCHITECTURE"
- "vamos popular o cérebro"
- continuação natural após `/ih-bootstrap`

## Modo de operação

Detecte qual modo:

- **Greenfield:** cérebro vazio (ou só com placeholders `<Nome do Produto>`) e nenhum repo de código associado mencionado. Só perguntas.
- **Retroativo:** o autor mencionou um repo de código existente (no `/ih-bootstrap` ou agora). Extraia factual do código antes de perguntar.

Se ambíguo, pergunte: "Esse produto já tem código rodando em algum repo? Se sim, qual o caminho — extraio stack/data model de lá antes de perguntar."

## Ordem dos arquivos (fixa)

1. **BUSINESS.md** — problema, cliente, modelo de negócio
2. **CONTEXT.md** — visão, stack, ambientes, repos, V1
3. **ARCHITECTURE.md** — arquitetura, data model, restrições
4. **REQUIREMENTS.md** — RF e RNF
5. **UX.md** — experiência + fluxo principal em Mermaid
6. **DESIGN.md** — decisões de design + links

Para cada arquivo, abra a seção, pergunte os campos que faltam, marque `[PREENCHER]` no que o autor não souber agora, mostre o diff, espere OK, segue pro próximo.

## Modo retroativo: extração

Antes de perguntar sobre CONTEXT.md ou ARCHITECTURE.md:

1. Liste arquivos relevantes do repo de código: `package.json`, `pyproject.toml`, `Gemfile`, `composer.json`, `docker-compose.yml`, `prisma/schema.prisma`, migrations, `.env.example`.

2. Extraia:
   - **Stack** (frontend, backend, banco, infra) — do package manager + Dockerfile
   - **Modelo de dados** — do Prisma/migrations/models
   - **Ambientes** — de variáveis em `.env.example` e arquivos de deploy

3. Mostre ao autor o que extraiu:
   > "Olhei o código e extraí isso. Confirma?"
   > [lista factuais]

4. Marque o que veio do código vs o que veio do autor vs `[PREENCHER]` em cada arquivo. Use comentários sutis.

## Princípios

- **Perguntas dirigidas, não free-form.** Em vez de "fala do produto", peça campo a campo do template.
- **`[PREENCHER]` honesto é melhor que invenção.** Se o autor não sabe, marca e segue.
- **Um arquivo por vez.** Não enchurra o autor com 6 arquivos juntos.

## Fechamento

No fim, mostre o resumo:
- Quantos arquivos populados
- Quantos `[PREENCHER]` ficaram (lista)
- Próximas skills sugeridas: `create-feature` (pra modelar features) e `linearize-feature` (quando houver features prontas)

Único commit ao final:

```bash
git add BUSINESS.md CONTEXT.md ARCHITECTURE.md REQUIREMENTS.md UX.md DESIGN.md
git commit -m "feat: foundation do cérebro"
```

## Exemplos de prompts que ativam

- "Vamos modelar o cérebro do <produto>"
- "Preciso preencher BUSINESS e CONTEXT"
- "Esse produto já tem código em /repos/sight — alimenta o cérebro a partir dele"

## Exemplos que NÃO ativam

- "Cria a feature de checkout" → create-feature
- "Decidi usar Supabase" → register-decision
