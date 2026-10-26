# agent-spec — kit **só Cursor** (sem Claude Code)

Pacote portátil do framework agent-spec para um projeto que **não usa Claude Code**.

- Roda no **Agent chat do Cursor** com `/agent-spec-...` (mesma UX de slash do terminal Claude)
- Orquestra com **`Task`** (subagents), não com `Agent()` do Claude Code
- **Não precisa** instalar Claude Code, nem CLI Anthropic, nem pasta “só porque tem Claude no nome”

A pasta `.claude/` neste kit é **só convenção de path do framework** (skills/rules históricas apontam para ela). O Cursor já lê `.claude/skills` e `.claude/agents`; o kit também espelha tudo em `.cursor/` para descoberta nativa.

## Conteúdo

```
.cursor/
  skills/agent-spec-*/     # skills (Cursor-native)
  skills/_shared/          # host-compat Cursor-only
  agents/                  # gates + template de executor
  rules/agent-spec-*.mdc   # rules no formato Cursor
.claude/
  skills/ + agents/ + rules/   # espelho canônico (paths citados pelas skills)
AGENTS.md                  # instruções do projeto no Cursor
CLAUDE.md.template         # opcional — várias skills ainda citam CLAUDE.md
```

## Instalação (projeto novo, só Cursor)

```bash
DEST=/caminho/do/projeto-novo

cp -a templates/agent-spec-cursor-kit/.cursor  "$DEST/"
cp -a templates/agent-spec-cursor-kit/.claude  "$DEST/"
cp templates/agent-spec-cursor-kit/AGENTS.md   "$DEST/"

# Recomendado: criar CLAUDE.md a partir do template (skills/gates ainda referenciam)
cp templates/agent-spec-cursor-kit/CLAUDE.md.template "$DEST/CLAUDE.md"
# edite CLAUDE.md com stack/visão do projeto novo
```

Ou pelo tarball:

```bash
tar -xzf templates/agent-spec-cursor-kit.tar.gz -C "$DEST" --strip-components=1
```

### Depois de copiar

1. Cursor → Agent chat → `/` → confirme `agent-spec-guide`.
2. Customize o executor: edite/renomeie `.cursor/agents/_TEMPLATE-stack-executor.md` (e a cópia em `.claude/agents/`). Remova `go-backend-implementer.md` se a stack não for Go.
3. `/agent-spec-generate-claude-md` e `/agent-spec-testing-stack-bootstrap` para rules da **sua** stack.
4. (Opcional) `/agent-spec-adr-bootstrap`.

## Uso

```
/agent-spec-guide
/agent-spec-pre-refinement
/agent-spec-taskcard-generate …
/agent-spec-taskcard-run … <seu-executor>
/agent-spec-sdd-run-tasks … <seu-executor>
```

Argumentos = texto na mesma mensagem após o nome da skill.

Binding: `.cursor/skills/_shared/host-compat-cursor.md` (Cursor-only — sempre `Task` + pergunta no chat).

## Executar igual ao Claude

No **Agent chat** do Cursor, digite o **mesmo** comando do terminal Claude:

```
/agent-spec-guide
/agent-spec-sdd-run-tasks docs/specs/features/<feature>/v1/task_plan.md <seu-executor>
/agent-spec-taskcard-run path/da/card.md <seu-executor>
```

Cheat sheet completo: `AGENTS.md` na raiz do kit.

## O que este kit NÃO inclui

- Claude Code / commands exclusivos Anthropic
- Rules de stack deste repo Go (`go-backend-conventions`, etc.) — gere no projeto novo
- Plugin/marketplace

## Frameworks

| Framework | Quando | Pipeline |
|---|---|---|
| SDD | Feature grande | PRD → Tech Spec → Task Plan → Run |
| miniSpec | Feature média | Intent → Scope → Tasks → Run |
| TaskCard | Tarefa pontual | TaskCard → Run |
| ADR | Decisões | create / list / show / … |

`*-run*` = **executor → Gate 1 QA → Gate 2 Tech Review**.
