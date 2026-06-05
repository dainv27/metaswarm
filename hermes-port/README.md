# metaswarm — Hermes Port

Đây là bản **port** của [metaswarm](https://github.com/dsifry/metaswarm) — multi-agent orchestration framework — để chạy trên **Hermes Agent** thay vì Claude Code / Gemini CLI / Codex CLI.

## Sự khác biệt với metaswarm gốc

| Tính năng | metaswarm (gốc) | Hermes Port|
|-----------|-----------------|------------|
| Agent runtime | Claude Code subagents | Hermes delegate_task |
| Agent definitions | `.md` files trong `agents/` | Profile `.md` files trong `hermes-port/profiles/` |
| Skills | Claude Code skills | Hermes skills |
| Commands | Slash commands | Hermes skills + terminal |
| Knowledge base | BEADS JSONL | Hermes memory + knowledge files |
| Review gates | Parallel subagents | Parallel delegate_task |
| Task tracking | BEADS CLI | Todo tool + git commits |
| Diagrams | Mermaid (GitHub rendered) | Mermaid (GitHub rendered) ✅ |

## Cấu trúc thư mục

```
hermes-port/
├── profiles/           # 18 agent profiles (system prompts)
│   ├── issue-orchestrator-agent.md
│   ├── architect-agent.md
│   ├── researcher-agent.md
│   ├── coder-agent.md
│   ├── cto-agent.md
│   ├── code-review-agent.md
│   ├── security-auditor-agent.md
│   ├── product-manager-agent.md
│   ├── designer-agent.md
│   └── ... (thêm nữa)
├── skills/             # Hermes skills
├── docs/               # Documentation
└── README.md           # File này
```

## Cách sử dụng

### 1. Trigger Issue Orchestrator

Khi bạn muốn bắt đầu làm việc trên một task/issue, gọi Issue Orchestrator:

```
/orchestrator Bắt đầu làm việc trên issue: <mô tả task>
```

Issue Orchestrator sẽ:
1. Gọi **delegate_task** để spawn Researcher Agent
2. Gọi **delegate_task** để spawn Architect Agent
3. Gọi **delegate_task** song song cho Design Review Gate (5 agents)
4. Chạy Orchestrated Execution Loop với Coder, Reviewer, Security Auditor
5. Tạo PR và monitor

### 2. Gọi trực tiếp từng agent

Bạn cũng có thể gọi trực tiếp từng agent qua delegate_task:

```python
delegate_task(
    goal="<mô tục đích công việc>",
    context="<context cần thiết>",
    toolsets=["terminal", "file"],
    role="leaf"
)
```

Với mỗi agent, system prompt sẽ được load từ file profile tương ứng.

## Workflow

```
User Issue / Task Description
            │
            ▼
┌─────────────────────────────┐
│   Issue Orchestrator         │
│   (Hermes delegate_task)     │
└──────────────┬──────────────┘
               │
    ┌──────────┼──────────┐
    ▼          ▼          ▼
Research    Plan      Design Review
(parallel)  (parallel) (5 agents parallel)
    │          │          │
    └──────────┼──────────┘
               ▼
    Work Unit Decomposition
               │
               ▼
    Orchestrated Loop (per unit):
    IMPLEMENT → VALIDATE → REVIEW → COMMIT
    (Coder)    (Orchest.) (Parallel) (Git)
               │
               ▼
    Final Review → PR → Shepherd → Closure
```

## Agents

### Core Agents

| Agent | Profile | Vai trò |
|-------|---------|---------|
| Issue Orchestrator | `issue-orchestrator-agent.md` | Điều phối toàn bộ workflow |
| Researcher | `researcher-agent.md` | Khám phá codebase, tìm patterns |
| Architect | `architect-agent.md` | Thiết kế implementation plan |
| Coder | `coder-agent.md` | TDD implementation |

### Review Agents (Design Review Gate)

| Agent | Profile | Vai trò |
|-------|---------|---------|
| CTO | `cto-agent.md` | TDD readiness, codebase alignment |
| Product Manager | `product-manager-agent.md` | Use case, user benefit, scope |
| Designer | `designer-agent.md` | UX/API design, consistency |

### Review Agents (Execution Phase)

| Agent | Profile | Vai trò |
|-------|---------|---------|
| Code Review | `code-review-agent.md` | Code quality, conventions |
| Security Auditor | `security-auditor-agent.md` | OWASP, vulnerabilities |

## Design Principles (giữ nguyên từ metaswarm)

1. **Knowledge-Driven Development** — Agents prime từ knowledge base trước mỗi task
2. **Trust Nothing, Verify Everything** — Orchestrator tự validate, không tin subagent
3. **Parallel Review Gates** — Independent reviewers chạy song song
4. **Recursive Orchestration** — Orchestrator spawn sub-orchestrator cho complex tasks
5. **Test-First Always** — TDD bắt buộc, coverage enforced
6. **Git-Native Everything** — Mọi thứ trong version control
7. **Human-in-the-Loop** — Checkpoints tại critical boundaries

## Cài đặt

1. Copy thư mục `hermes-port/` vào project của bạn
2. Load khi cần qua `skill_view()` hoặc đọc trực tiếp từ file

## Cảnh báo

- Hermes Port này là bản **adaptation** — không phải official port
- Một số tính năng của metaswarm (BEADS integration, Claude Code specific features) cần được adapt cho Hermes
- Đảm bảo bạn có đủ `delegate_task` quota để chạy multi-agent workflow

## License

MIT (giữ nguyên từ metaswarm gốc)
