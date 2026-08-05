# Agent team

Custom agents for building Mona's Project Pulse dashboard. Orchestration runs via **GitHub Copilot CLI in a Codespace**.

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates Planner, Coder, and Designer; breaks work into phases, assigns file scopes, runs parallel/sequential tasks, and reports outcomes. Does not implement code. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repo and docs; produces implementation plans with steps, file assignments, dependencies, edge cases, and validation. Does not write code. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements logic, fixes bugs, and builds runnable app support (e.g. `.vscode/launch.json` for Project Pulse). Stays in assigned files and validates changes. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Owns UI/UX, accessibility, layout, and visual design for a polished Project Pulse dashboard (cards, badges, responsive CSS). | `.github/agents/designer.agent.md` |

**Flow:** Orchestrator → Planner plan → phased Coder/Designer work → integrated result. Git stays with the learner (agents do not commit or push).
