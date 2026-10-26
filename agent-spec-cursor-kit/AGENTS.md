# Executar agent-spec no Cursor (igual ao Claude)

Use o **Agent chat** (não Ask/Composer puro). Digite **exatamente** o mesmo `/comando` do terminal Claude.

## Cheat sheet 1:1

| Claude Code (terminal) | Cursor (Agent chat) |
|---|---|
| `/agent-spec-guide` | `/agent-spec-guide` |
| `/agent-spec-pre-refinement` | `/agent-spec-pre-refinement` |
| `/agent-spec-taskcard-generate …` | `/agent-spec-taskcard-generate …` |
| `/agent-spec-taskcard-run PATH go-backend-implementer` | `/agent-spec-taskcard-run PATH go-backend-implementer` |
| `/agent-spec-sdd-generate-prd …` | `/agent-spec-sdd-generate-prd …` |
| `/agent-spec-sdd-run-tasks PATH go-backend-implementer` | `/agent-spec-sdd-run-tasks PATH go-backend-implementer` |
| `/agent-spec-minispec-run-tasks PATH go-backend-implementer` | `/agent-spec-minispec-run-tasks PATH go-backend-implementer` |
| `/agent-spec-debt-resolution PATH` | `/agent-spec-debt-resolution PATH` |

**Args:** na mesma mensagem, depois do nome da skill (igual Claude).

## Exemplos prontos (este repo)

```
/agent-spec-guide

/agent-spec-sdd-run-tasks docs/specs/features/cadastro-usuario/v1/task_plan.md go-backend-implementer

/agent-spec-taskcard-run docs/specs/features/<feature>/v1/tasks/<card>.md go-backend-implementer
```

## O que muda por baixo (você não digita isso)

| Claude Code | Cursor |
|---|---|
| `Agent(...)` | `Task` (subagent) |
| `AskUserQuestion` | pergunta no chat |
| `$ARGUMENTS` | texto após `/skill` |

Binding: `.claude/skills/_shared/host-compat-cursor.md`.

## Fluxo de um `*-run*` (igual Claude)

1. Você invoca `/agent-spec-…-run…` + args  
2. Orquestrador lê a skill + host-compat  
3. `DISPATCH` → executor (`go-backend-implementer`)  
4. Gate 1 → `agent-spec-qa-validator`  
5. Gate 2 → `agent-spec-staff-architecture-review`  
6. Débito médio/baixo → `qa-observations.md`
