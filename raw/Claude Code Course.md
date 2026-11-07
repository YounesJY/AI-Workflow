# Claude Code Course

=============================

= How to Install & initialize

=============================

-> You'll need claude subscription or claude console account (billed-based API usage) for this, NO FREE PLAN

-> go to the website and get the install cmd from there

-> **<mark>DO NOT USE Billed-based API USAGE</mark>** you could spend 500$ in one session without noticing that, **<mark>ONLY USE YOUR CLAUDE SUBSCRIPTION</mark>**

-> **<mark>NEVER RUN ANY AI AGENT WITHINH YOUR HOME FOLDER</mark>**, this will lead to give full access to your data and might lead to accidential data corruption (antigraviry accident with 2TB lost), this also lead to context pollution since it'll need to search your home tree for the project folder

-> **<mark>ALWAYS</mark>** open the agent on your project folder only

-> NEXT.js is mosly prefered by AI and easy for beginners



=============================

= Agent Sessions Management

=============================

`claude --resume/-r` to resume one of the last session after your have exist it (ctrl+c 2x or /exit)

`rewind` to restore a conversation/code at somepoint and revert changes



=============================

= CLAUDE.md file

=============================

-> **<mark>THE MOST IMPORTANT FILE IN YOUR PROJECT</mark>**

-> **The very 1st file being loaded into yout agent context**

-> **The very 1st file red and undestood by your agent**, it's the portal to your project

-> the more to take care of this file, the more your life you and the agent will be

-> **You can just call it AGENTS.md** if you're using other agents (codex, opencode)

-> Can be created manually or via /init

-> after creating file, being reviewed and verefied. you should add **<mark>## IMPORTANT NOTES</mark>** keep this file uptodate with major changes on the project and it's status



=============================

= Permissions - **3 Modes**

=============================

-> The agent asks you everytime - the safest one

-> shift + tab for `accept-edits-on` for commandes and not edits, no compound, compact .. - still safe

-> **<mark>claude dangerously-skip-permissions</mark>** for bypass permissions - <mark>very dangerous</mark>

-> **<mark>ONLY</mark>** available on shift+tab whenever you launch it with that command



=============================

= costume Permissions

=============================

-> you can configure claude with auto-allowed cmds, ask and deny

-> you can use /sandbox **but it's for more advanced users**



=============================

= Built-in and costume cmds

=============================

-> /add-dire to give claude access to that new dir which is **outside** of your current working dir and remember it in that session

-> /chrome to link it with chrome and see the frontEnd results etc..., **BUT** there's a better way and more professional and minimal
