Agent skill for running structured debate between two subagents, followed by neutral consolidation. Tested in GH Copilot and Cursor, works great when combining 2 different SOTA models, such as Opus 4.6 and GPT 5.4.

## How To

Copy the skill, optionally copy Curosr of GH agent defition that define model ids to be used in the debate.

- Skill
  - `.agents/skills/multi-agent-debate` - copy to your repo, should be picked up by you agent (you might want to check if your agent can pick up .agents/ or you might want to change that path to smth like `.claude` )

- Agent/Modle definitions
  - The skill references agents by names, `opus-max` and `gpt-max`, your agent harness might search for agents with that names, if not found it will likely revert to spawning subagents reusing the parent mode
  - `.cursor` and `.github` folders contain lean definitions of the abovementioned agents, their sole purpose is not role play or instructions BUT rather the definition of models to be used by the skill
  - Cursor and GitHub Copilot have different model naming hence the need to define agents twice, those are essentially shortcuts to get started fatser
  - Feel free to change to other models as you deem necessary