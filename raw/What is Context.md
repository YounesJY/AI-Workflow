# What is Context

-> LLMs take input tokens to generate on putput token, then goes in a loop of input+output token until reach a stop token

-> during a session, the LLM takes your prompt + the WHole sessions (all conversations) as an input



LLMs has no brain/awarness, no thinking and no memory



==========================

= Why Context is important

==========================



Context = ***details***, the more details an LLMs is given, the more smart/accurate becames

Be precise in your prompts, no detailsless/ambyguis prompts, LLMs won't understand your intent



-> LLMs context isn't Unified due to the **<u>Context Rot</u>**



=============

= Context Rot

=============

The more longer a session is , the more a context is filled, the less LLMs performance becames



Cotext is better to be around 40%~50%, 



====================

= Lost In The Middle

====================

Focus chart take **U** form

The systme prompt + the last message is the more important for an LLM, they're the more focused parts

The more longer a context is , the more details becaume lost in the middle

the less a contex is , the more LLM accurate/precise/smart is



===================================

= How to better manage your context

===================================

-> **<mark>ALWAYS</mark>** revert the Side Quetions and randam qurtion that aren't related to your project

-> **<mark>Handoff</mark>** , ask the Agnet to write dons has he has done (with needed details) to a handoff.md then opne a new session and ask the new agent to read the HandOff for a nea clean context

-> **<mark>Compaction</mark>** same as HandOff but **Automated** + /compact with your prompt to be compacted with saving some details

-> **<mark>SubAgents</mark>** (**<u>The most imporant feature</u>**) subagents saves context **BUT** they consume tokens (credits)

-> **<mark>AGENT.md/CLAUDE.md</mark>** (**<u>a very important guidance file</u>**) a file like **FAQ**, that saves your agent a lof of randomness and guide it directly to what he needs without spending a lot of context/tokens in sides quetions ,it contains exactly what the agnet needs to know and where to earch



==================

= Context mistakes

==================

MAKE YOUR PORMPTS clear and full of needed details, append secreenshots, files, details, references, ...

your agent won't be ablde to reason by itself

-> **Try <mark>claude-context-visualer tool</mark>** so you know exaclt where your token cosumption goes, and what parts fo you AI workflos needs optimisations
