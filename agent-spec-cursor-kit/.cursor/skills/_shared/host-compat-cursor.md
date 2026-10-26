# Host compatibility — Cursor (execução igual ao terminal Claude)

> Este projeto roda agent-spec no **Agent chat do Cursor**, com a mesma UX de `/skill args` do Claude Code.
> **Não exige Claude Code.** Orquestradores usam sempre `Task` + pergunta no chat.

## Host

**Sempre Cursor** (`Task`). Não use `Agent()` nem `AskUserQuestion`.

Se `Task` não existir → pare e avise que o Agent chat do Cursor com subagents é obrigatório.

## Binding (igual ao Claude, API do Cursor)

| No Claude Code você fazia | No Cursor faça |
|---|---|
| `/skill-name args…` no terminal | `/skill-name args…` no **Agent chat** |
| `Agent(subagent_type=X, …)` | `Task` com `subagent_type=X`, prompt em `prompt` |
| `AskUserQuestion` | Pergunte no chat (opções numeradas) e aguarde |
| Omitir `subagent_type` (default) | `subagent_type = "generalPurpose"` |
| `$ARGUMENTS` | Texto após o nome da skill na mensagem |

## Modelos

| Shorthand no SKILL | `model` no `Task` |
|---|---|
| `sonnet` | `claude-sonnet-5-5-high` ou `inherit` |
| `opus` / `opus[xhigh]` | `claude-opus-4-8-thinking-high` |
| Sem override | `inherit` |

## Pseudocódigo

```
DISPATCH(subagent_type, prompt, model?):
  Task(subagent_type=<tipo ou "generalPurpose">, prompt=prompt, model=mapa(model)|inherit)

ASK(pergunta, opções[]):
  pergunta + opções numeradas no chat; PAUSE até resposta

ARGS := texto da mensagem após o nome da skill (trim)
```

## Descoberta do executor

1. Liste `.claude/agents/*.md` (e `.cursor/agents/` se existir).
2. Remova: `agent-spec-qa-validator`, `agent-spec-staff-architecture-review`, `agent-spec-qa-test-generator`.
3. `ASK` com agentes + `"Default (orquestrador genérico)"`.
4. Default → `generalPurpose`.

## Gates

| Papel | `subagent_type` |
|---|---|
| Gate 1 — QA | `agent-spec-qa-validator` |
| Gate 2 — Tech Review | `agent-spec-staff-architecture-review` |
| CTs | `agent-spec-qa-test-generator` |
