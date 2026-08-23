# Agent team

Mona's Project Pulse dashboard is built by a coordinated custom-agent team, orchestrated through the GitHub Copilot CLI in a Codespace.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks the work into phases, delegates scoped tasks to the specialists, coordinates dependencies, and verifies the integrated result without implementing it directly. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and relevant documentation, identifies requirements and risks, then creates an implementation plan with file ownership, dependencies, edge cases, and validation expectations. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements scoped dashboard functionality with explicit, testable behavior, including runnable-app support such as the Project Pulse launch configuration when assigned. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Defines and implements the dashboard's UI/UX, accessibility, information hierarchy, responsive layout, and polished Project Pulse visual treatment. | `.github/agents/designer.agent.md` |

The Orchestrator starts with the Planner's technical plan, assigns non-overlapping work to the Coder and Designer in the appropriate order or in parallel, then confirms the pieces work together. Git operations remain under the learner's control through Copilot CLI prompts.
