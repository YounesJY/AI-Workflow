# What is SKILL in agents

Skills are just a a long guidance prompt that saves you a lot of tokens and gives better reulst

Agent context only load the skill name & description

the a skill has SKILL.md (mandatory) and more optional assets

The skill.md as the metadate secion as the most important part

skill name in english with kababCase

skill name = foldername

Description is the **Most Critical part**, not too simple that agent alwyas use it and fullfit the context, and too complex and never used

-> Skill promts won't be loaded to agent context unless this skil needed

-> use **references** to reduce context window usage as Devide&conquer so agent knows what he needs rather than **readinf the full Skill MD**

store skills in ~/Projects/YourPrpject/.claude/skills  or .agents/skills

-> claude has an additional fields "allowed-tools", "model"

-> openclaw magic is SKILLs

->100 skills = 1 MCP tool

==================

Most Important skills

- Agent-browser

- Grill-Me

- FrontEnd Design

- Vercel-React 2 skills (best practices and skills)

- Anthropic common skills
