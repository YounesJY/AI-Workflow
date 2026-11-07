# open code course

-> what is open code ?

what's diff between opencdoe/claude code (harness) and providers (anthropic, OpenAI, Azure..) and the models

-> intalling open code

-> how to open it within a project

-> connect to a provider using account/API key

-> arabic vs en glish token consumption

-> AI thinking/Though

-> switch between modes Build/Plan

-> Start new session

-> alywas start with Plan Mode since it's read only (no update, no delete)

-> how to make a good promps (clear ideas, details + last statment should be kinda "ask me quetions to make sure you undertand..." since you shoudnlt assume the agaent knows your intention)

-> Quetions PopUps in opencode and how to navigate them

-> Plan the Build is ONLY for simple projects/tools

-> Open New session and ask to read @Plan.Md or past files to givr more context

-> ask the AI to run the app and give you the URL to check

-> paste Errors or take a screenshot for better help

-> Undo and Redo changes as one of the most imporatnt

==========

= Sessions 

==========

-> Session Share Unshare

-> Session continue without opencode > /session > search via opencode -c or opencode -s ID

-> Session Fork ? Advanced ? 

-> Session Context and plution and losing memory ?

-> Session Compaction (recommended when reaching 40%-50%) as a resume 

-> Export session (advanced)

-> /editor to opn external editor

-> /models

-> /timeline (Very Important for navigation)

-> /variants (model level) (Low/Medium for small/simple tasks-features Low/None for rediculess things)

-> cycle Thour viaraits (Ctrl+T) / OpenNew terminla tab ctrl+shift+T for multitasking

=================

= Desktop Version

=================

-> Avialable for all Platforms

-> go settings > Buttom most and enable show agents/ sererv status / cmd pallet

-> DEsktop app has multi sessions capability due to taps (same in terminal)

-> show diff in desktop app, naviagte files, direct modification on spesific lines

=================

= Primary & Sub agents

=================

-> Modes aren't modes actaully , thery're primary agents

-> Primary agents are meante to be talked to directly + Spawn subAgents

![](/home/unisjy/.config/marktext/images/2026-11-01-15-13-43-image.png)

-> They called Agents not modes because you can add many agants as you want

-> you can have a Manager Agent called the Orchestrator

-> The Ochestartuf have neraly full permissions, but mainly abulity to spawn sub agents, no write/modify files permission

-> You can't walkd directly with subAgents, 

- Exploer Agent: @explore To explore your project files & structure (so pripary agent context stays clean)

- Scout Agent: To reach out to the docs,Readme, best practices ...

- General Purpose: @general

-> A hybed Agent : Bug Hunter is both a primary agent (you can talk too) and a subAgent (can be spawned by primary agents)

-> You can't use primary agents with @ to spawn them on promt, it's onlt for subAgents

-> You can use "Multiple @subAgents" if you need (does this needs credits ?)

-> ~Delegating in parallel

-> Ctrl+X to View SubAgents during work

-> /Agents to veiw primary Agents

-> "`opencode agent create`" to create a new primary agent

- Location (Local/Global)

- Description

- Permissions (read, edit ,bash, ListFiles, taskToSpawnAgents, webfetch & webSerch, todoTasks, LSP, Skills)

- Agent Mode

waht's is SOC2 ?

-> Pres Esc 2x to intererpt agent work

-> **~/.config/opencode/agents** for professional agent configue

=============

= Skill

=============

`bunx skills find` to install skills

opencode uses .agents/

skill permissions are advanced topic

============

= MCP servers

============

are just tools used by your Agent

MCPs really consume context, uses just the needed ones

============

= Plugins

============

99% of the time you won't use it,

too advanced
