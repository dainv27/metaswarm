# Metaswarm Port Notes

Port of https://github.com/dainv27/metaswarm for Hermes Agent (OWL).

## Mapping: metaswarm -> Hermes

| metaswarm Concept | Hermes Equivalent |
|---|---|
| Claude Code Task() API | `delegate_task()` |
| Agent Skills (SKILL.md) | Hermes skills (`~/.hermes/skills/`) |
| BEADS (`bd` CLI) | Markdown files / JSONL (not ported, bd requires Go) |
| Claude Code hooks | Hermes event system |
| Worktree per agent | `delegate_task` with isolated context |
| Subagent prompt | `context` field in `delegate_task` |

## Skills Created

| Skill | Path | Status |
|---|---|---|
| `metaswarm-start` | `skills/metaswarm/metaswarm-start/SKILL.md` | Done |
| `orchestrated-execution` | `skills/metaswarm/orchestrated-execution/SKILL.md` | Done |
| `design-review-gate` | `skills/metaswarm/design-review-gate/SKILL.md` | Done |
| `plan-review-gate` | `skills/metaswarm/plan-review-gate/SKILL.md` | Done |
| `pr-shepherd` | `skills/metaswarm/pr-shepherd/SKILL.md` | Done |
| `handling-pr-comments` | `skills/metaswarm/handling-pr-comments/SKILL.md` | Done |
| `create-issue` | `skills/metaswarm/create-issue/SKILL.md` | Done |
| `brainstorming-extension` | `skills/metaswarm/brainstorming-extension/SKILL.md` | Done |
| `external-tools` | `skills/metaswarm/external-tools/SKILL.md` | Done |
| `visual-review` | `skills/metaswarm/visual-review/SKILL.md` | Done |
| `setup` | `skills/metaswarm/setup/SKILL.md` | Done |
| `status` | `skills/metaswarm/migrate/SKILL.md` (co-located) | Done |

## Key Adaptations

1. **No BEADS**: Task tracking via markdown/JSONL files in project
2. **delegate_task spawning**: Each agent is a `delegate_task` call with isolated context
3. **Orchestrator pattern**: Main agent (Orchestrator) never writes code directly
4. **Quality gates**: Blocking transitions enforced by Orchestrator

## Usage

1. User triggers metaswarm workflow (issue or task)
2. Load `metaswarm-start` skill
3. Follow 9-phase workflow
4. Use `delegate_task` to spawn agents
5. Orchestrator validates everything independently

## TODO

- [ ] Install BEADS CLI when Go is available
- [ ] Add worktree management helpers
- [ ] Customize agent roster for project needs
- [ ] Add Slash commands (if Hermes supports)
- [ ] Test full workflow end-to-end
- [ ] Add more review rubrics

## References

- Original: https://github.com/dainv27/metaswarm
- Agent definitions: `metaswarm/agents/`
- Skill definitions: `metaswarm/skills/`
- Rubrics: `metaswarm/rubrics/`
