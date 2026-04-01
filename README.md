Agent skill for running structured debate between independent high-capability subagents, followed by neutral consolidation. The default path is a 2-agent debate between `gpt-max` and `opus-max`. If the user explicitly asks for three agents or a three-way debate, the skill switches to 3-agent mode and adds `gemini-max` as Branch C.

<img width="654" height="774" alt="image" src="https://github.com/user-attachments/assets/c9bc5126-084d-431b-9278-f445c9a7947f" />

Tested in GitHub Copilot and Cursor. It works well when combining different high-capability models, such as Opus and GPT in the default mode, with Gemini added only for explicit 3-agent runs.

## What Is Included

- `.agents/skills/multi-agent-debate/SKILL.md` is the entrypoint.
- `.agents/skills/multi-agent-debate/references/two-agent-mode.md` defines the default workflow.
- `.agents/skills/multi-agent-debate/references/three-agent-mode.md` defines the explicit 3-agent workflow.
- `.agents/skills/multi-agent-debate/references/protocol-artifacts.md` contains the shared run schemas.
- `.github/agents` contains ready-to-use `gpt-max`, `opus-max`, and `gemini-max` definitions for GitHub Copilot.
- `.cursor/agents` contains `opus-max`, `gemini-max`, and an extra `opus` definition; adapt the names there if your Cursor setup needs different aliases.

## Mode Selection

- Use 2-agent mode by default.
- Switch to 3-agent mode only when the user explicitly asks for 3 agents, three agents, or a three-way debate.
- The consolidator is always a separate neutral pass, not one of the debating branches.
- The skill keeps unresolved high-impact disagreement visible instead of flattening it into false consensus.

## How To Use

1. Copy `.agents/skills/multi-agent-debate` into your repo.
2. If your agent harness resolves named agents from local files, also copy the agent-definition folder that matches your environment and adapt the names if needed.
3. Keep the referenced agent names aligned with your environment: `gpt-max`, `opus-max`, and `gemini-max` for explicit 3-agent runs.
4. Invoke the skill when the task has legitimate competing interpretations, meaningful trade-offs, or uncertainty that should stay visible.

## Notes On Agent Definitions

- The skill references agent names, not raw model ids.
- GitHub Copilot and Cursor use different model naming, so this repo includes parallel definitions where available.
- Cursor users may still need to add or rename agent files so the skill can resolve `gpt-max`, `opus-max`, and `gemini-max` in their own setup.
- If a referenced agent is missing, your harness may fall back to the parent mode. For explicit 3-agent runs, do not silently downgrade when `gemini-max` is unavailable.
- Feel free to swap the underlying models as needed.
