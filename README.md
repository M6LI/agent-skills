# GitHub Copilot agents for JetBrains / IntelliJ

Place the `.github/agents` directory into the **root** of a repository and open the repository in IntelliJ with GitHub Copilot Chat. Agent profiles use `.agent.md` Markdown files with YAML frontmatter.

## Included agents

| Agent | Intent | Editing allowed by its tools? |
| --- | --- | --- |
| `planner` | Architecture and implementation planning | No (`read`, `search`) |
| `builder` | Implementing and validating changes | Yes (`edit`, `execute`) |
| `reviewer` | Independent general code audit | No edit tool; `execute` can change files, so instructions prohibit tracked-source edits |
| `ml-reviewer` | ML/quant methodology and implementation audit | No edit tool; diagnostics may execute |
| `ui-reviewer` | UX, accessibility, layout and visual consistency audit | No edit tool; diagnostics may execute |
| `research-explorer` | Technical research and approach comparison | No (`read`, `search`, `web`) |

Tool availability can vary by Copilot environment/version. `tools` settings restrict tools, but instructions alone are not a security sandbox. The `web` tool may not be available in all JetBrains setups.

## Setup

1. Copy `.github/agents/*.agent.md` to the same relative path in your repository.
2. Update the GitHub Copilot plugin; open Copilot Chat in IntelliJ in **Agent** mode.
3. In **Tools > GitHub Copilot > Edit Settings > Chat**, enable **Enable Subagent**.
4. Select agents from Copilot Chat's agent dropdown if needed, or ask the main agent to delegate to a named subagent.
5. Keep the **main agent** responsible for coordination. Subagents cannot delegate to further subagents.

## Prompts to try in the **main agent** (not inside a subagent)

### Implement + independently review

> Implement [feature]. Delegate the design and acceptance criteria to **planner**. Implement using **builder** or the main session. Then delegate an independent diff review to **reviewer**. Resolve high-severity findings and report actual tests and outstanding risks.

### ML/quant experiment

> Investigate and implement [model/experiment]. Ask **planner** to define the train/test protocol and implementation plan. Implement the smallest comparison against the baseline, then use **ml-reviewer** to audit point-in-time feature availability, temporal leakage, metric validity and production inference parity. Report what was truly verified.

### Frontend

> Implement [UI change]. Follow any repo-specific UI `SKILL.md`. Have **ui-reviewer** audit the resulting diff and rendered preview if available. Fix any verified usability or accessibility problems.

### Parallel research

> Delegate independent research questions to separate **research-explorer** subagents. Ask each to compare against the same baseline and return cited evidence and a falsifiable minimal experiment. Synthesise their findings in the main session. Do not edit shared files in parallel.

## Notes

- A custom `builder` is optional: the main Agent session can implement code directly. The file is included for explicit selection and consistent coding behaviour.
- Do not force multiple agents onto trivial edits; subagents add latency and token usage.
- `AGENTS.md` is a separate Codex-style convention. For repository-wide GitHub Copilot guidance, consider `.github/copilot-instructions.md`.
- GitHub Docs: https://docs.github.com/en/copilot/reference/custom-agents-configuration
- JetBrains custom agents: https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-custom-agents?tool=jetbrains
- JetBrains subagents: https://docs.github.com/en/copilot/how-tos/copilot-in-your-ide/use-copilot-agents/use-subagents?tool=jetbrains