---
type: raw
url: https://news.ycombinator.com/item?id=47640875
source: Hacker News
fetched: 2026-04-22
lang: en
points: 296
note: Karpathy LLM Wiki gist に対する HN メインスレッド。トップレベルコメントのみを保存（深い返信は raw には含めず、要約で扱う）
---

# HN thread 47640875: LLM Wiki – example of an "idea file"

- URL: https://news.ycombinator.com/item?id=47640875
- Points: 296
- Author: tamnd
- Created: 2026-04-04T16:57:45.000Z

## Top-level comments

### cyanydeez (2026-04-04)

Too much context pollution.

Start with short text context, and flow through DAGs via choose your own adventure. We alreadybreached context limits. Nows the time to  let LLMs build their contexts through decision trees and prune dead ends.

### mememememememo (2026-04-04)

This sounds like compaction for RAG.

### qaadika (2026-04-05)

> You never (or rarely) write the wiki yourself — the LLM writes and maintains all of it. You're in charge of sourcing, exploration, and asking the right questions. The LLM does all the grunt work — the summarizing, cross-referencing, filing, and bookkeeping that makes a knowledge base actually useful over time.

I'm not sure how you can get any closer to "turning your thinking over to machines." These tasks may be "grunt work," but it's while doing these things that new ideas pop in, or you decide on a particular or novel way to organize or frame information. Many of my insights in my (analog? vanilla? my human-written) Obsidian vault (that I consider my "personal wiki") have been made or expanded on because I happened to see one note after another in doing the "grunt work", or just by opening one note and seeing its title right beside a previously forgotten one.

There's nothing "personal" about a knowledge base you filled by asking AI questions. It's the AI's database, you just ask it to write stuff. Learn how to learn and answer your own damn questions.

Soon pedagogy will be a piece of paper that says "Ask AI."

I hate this idea that a result is all that matters, and the quicker you can get the result the better, at any cost (mental or financial, short-term or long-term).

If we optimized showers to be 20 seconds, we'd stop having shower thoughts. I like my shower thoughts. And so too my grunt-work thoughts.

---

As an aside, I'm not totally against AI writing in a personal knowledgebase. I include it at times in my own. But since I started my current obsidian vault in 2023 (now 4100 self-written notes, including maybe up to 5% Web Clipper notes), I've had a Templater (Obsidian plugin) template I wrap around anything AI-written to 'quarantine' it from my own words:

==BEGIN AI-GENERATED CONTENT==

<% tp.file.cursor(1) %>

==END AI-GENERATED CONTENT==

I've used this consistently and it's helped me keep (and develop) my own writing voice apart from any of my AI usage. It actually motivates me to write more, because I know I could always take the easy route and chunk whatever I'm thinking into the AI, but I'm choosing not to by writing it myself, with my own vocabulary, in my own voice, with my own framing. I trick myself into writing because my pride tells me I can express my knowledge better than the AI can.

I also manually copy and paste from wherever I'm using AI into my notes. Nothing automated. The friction keeps me from sliding into the happy path of turning my brain off.

### Vetch (2026-04-05)

This sounds very like Licklider's essay on Intelligence Amplification: Man Computer Symbiosis, from 1960:

> Men will set the goals and supply the motivations, of course, at least in the early years. They will formulate hypotheses. They will ask questions. They will think of mechanisms, procedures, and models. They will remember that such-and-such a person did some possibly relevant work on a topic of interest back in 1947, or at any rate shortly after World War II, and they will have an idea in what journals it might have been published. In general, they will make approximate and fallible, but leading, contributions, and they will define criteria and serve as evaluators, judging the contributions of the equipment and guiding the general line of thought.

> In addition, men will handle the very-low-probability situations when such situations do actually arise. (In current man-machine systems, that is one of the human operator's most important functions. The sum of the probabilities of very-low-probability alternatives is often much too large to neglect. ) Men will fill in the gaps, either in the problem solution or in the computer program, when the computer has no mode or routine that is applicable in a particular circumstance.

> The information-processing equipment, for its part, will convert hypotheses into testable models and then test the models against data (which the human operator may designate roughly and identify as relevant when the computer presents them for his approval). The equipment will answer questions. It will simulate the mechanisms and models, carry out the procedures, and display the results to the operator. It will transform data, plot graphs ("cutting the cake" in whatever way the human operator specifies, or in several alternative ways if the human operator is not sure what he wants). The equipment will interpolate, extrapolate, and transform. It will convert static equations or logical statements into dynamic models so the human operator can examine their behavior. In general, it will carry out the routinizable, clerical operations that fill the intervals between decisions.

https://www.organism.earth/library/document/man-computer-sym...

### kenforthewin (2026-04-05)

This is just RAG. Yes, it's not using a vector database - but it's building an index file of semantic connections, it's constructing hierarchical semantic structures in the filesystem to aid retrieval .. this is RAG.

On a sidenote, I've been building an AI powered knowledge base (yes, it uses RAG) that has wiki synthesis and similar ideas, take a look at https://github.com/kenforthewin/atomic

### mbreese (2026-04-05)

I’ve been doing something similar with a RAG system where in addition to storing the documents, we use an LLM to pull out “facts”. We’re using the LLM to look for relationships between different entities. This is then also returned when we query the database.

But I like the idea of an LLM generated/maintained wiki. That might be a useful addition to allow for more interactive exploration of a document database.

### atbpaca (2026-04-05)

An LLM that maintains a Confluence space. That looks like an interesting idea!

### argee (2026-04-05)

This is what Semiont is trying to do, to some extent [0].

Doesn't really feel that useful in practice.

[0] https://github.com/The-AI-Alliance/semiont

### devnullbrain (2026-04-05)

I don't see why this wouldn't just lead to model collapse:

https://www.nature.com/articles/s41586-024-07566-y

If you've spent any time using LLMs to write documentation you'll see this for yourself: the compounding will just be rewriting valid information with less terse information.

I find it concerning Karpathy doesn't see this. But I'm not surprised, because AI maximalists seem to find it really difficult to be... "normal"?

Rule of thumb: if you find yourself needing to broadcast the special LLM sauce you came up with instead of what it helped you produce, ask yourself why.

### gchamonlive (2026-04-05)

I don't think this is taking it as far as it can go.

Everything should live in the repo. Code and docs yes. But also the planning files, epics, work items, architectural documentation and decisions. Here is a small example of my Linux system doc: https://github.com/gchamon/archie/tree/main/docs

And you don't need to reinvent the wheel. Code docs can like either right next to it in the readme or in docs/ if it's too big for a single file or the context spams multiple modules. ADRs live in docs/architecture/decisions. Epics and Workitems can also live in the docs.

Everything is for agents and everything is for humans, unless put in AGENTS.md and docs/agents or something similar, and even those are for human too.

In a nutshell, put everything in the repo, reuse standards as much as possible, the idea being it's likely the structure is already embedded in the model, and always review documentation changes.

### voidhorse (2026-04-05)

This makes me feel like karpathy is behind on the times a tad. Many agent users I know already do precisely this as part of "agentic" development. If you use a harness, the harness is already empowered to do much of this under the hood, no fancy instruction file required. Just ask the agent to update some knowledge directory at the end of each convo, done. If you really need to automate it, write some scheduling tool that tells the agent to read past convos and summarize. It really is that easy.

### 0123456789ABCDE (2026-04-05)

this is so validating•

https://grimoire-pt5.sprites.app/

### nurettin (2026-04-05)

He really wants to shine, but how is this different than claude memory or skills? When I encounter something it had difficulty doing, or consistently start off with incorrect assumptions, I solve for it and tell it to remember this. If it goes on a long trial and error loop to accomplish something, once it works I tell it to create a skill.

### mpazik (2026-04-05)

Happy to see it gets attention. The friction shows up once you mix docs with structured things like work items or ADRs. Flat markdown doesn't query well and gets inconsistent. You can read TASKS.md fine. The agent can't ask "show me open tasks blocking this epic" without scanning prose or maintaining a parallel index.

The AGENTS.md approach papers over this by teaching the LLM the folder conventions. Works until the data gets complex but gets worse after many iterations.

Both are needed: files that open in any editor, and a structured interface the agent can actually query. Been building toward that with Binder (github.com/mpazik/binder), a local knowledge platform. 
Data lives in a structured DB but renders to plain markdown with bi-directional sync. LSP gives editors autocomplete and validation. Agents and scripts get the same data through CLI or MCP.

### Lockal (2026-04-05)

This thing already exists for multiple years - see https://deepwiki.com/ (99% it is autonomous, but it can be manually structured - see https://docs.devin.ai/work-with-devin/deepwiki#steering-deep...). There were also multiple attempts to replicate it with local LLMs.

The problem is that it is still a slop: not only it adds a lot of noise ("architecture" diagrams based on some cherry-picked filenames, incomplete datatables, hyperfocusing on strange things), it also hallucinates, adding factually incorrect information (while direct questions to LLM shows correct information).

### serendipty01 (2026-04-05)

Similar: https://v-it.org/doctrine/

### jdthedisciple (2026-04-05)

The challenge to me seems quality assurance:

I'd rather have it source the original document everytime, then an LLM-generated wiki which I most likely wouldn't have the time to fact-check and review myself.

### Imanari (2026-04-05)

Isn’t this just kicking the can down the road?

> but the LLM is rediscovering knowledge from scratch on every question

Unless the wiki stays fully in context now the LLM hast to re-read the wiki instead of re-reading the source files. Also this will introduce and accumulate subtle errors as we start to regurgitate 2nd-order information.

I totally get the idea but I think next gen models with 10M context and/or 1000tps will make this obsolete.

### estetlinus (2026-04-05)

Sounds like a solution in search for a problem.

### foo42 (2026-04-05)

I built a very similar system into my own assistant type project. In all honesty though I've not used it enough to know how well it works out in practice.

### ractive (2026-04-05)

I built a tool to exactly help an LLM navigate and search a knowledgebase of md files. It helps a lot to build such a wiki  by providing basic content search à la grep but also structured search for frontmatter properties. It also helps to move files around without breaking links and to fix links automatically. It is a CLI tool, mainly meant to be driven by AI tools.

Check it out: https://github.com/ractive/hyalo

### kmaitreys (2026-04-05)

> You never (or rarely) write the wiki yourself — the LLM writes and maintains all of it.

Then what is the point? Why be averse so to use your own brain so much? Why are tech bros like this?

### nidnogg (2026-04-05)

I've recently lazied out big time on a company project going down a similar rabbit hole. After having a burnout episode and dealing with sole caregiver woes in the family for the past year, I've had less and less energy to piece together intense, correct thought sequences at work.

As such I've taken to delegating substantial parts architecture and discovery to multiagent workflows that always refer back to a wiki-like castle of markdown files that I've built over time with them, fronted by Obsidian so I can peep efficiently often enough.

Now I'm certainly doing something wrong, but the gaps are just too many to count. If anything, this creates a weird new type of tech debt. Almost like a persistent brain gap. I miss thinking harder and I think it would get me out of this one for sure. But the wiki workflow is just too addictive to stop.

### gbro3n (2026-04-05)

I built AS Notes for VS Code (https://www.asnotes.io) with the option for this usage pattern in mind. By augmenting VS Code so it has the tooling we use in personal knowledge management systems, it makes it easy to write, link and update markdown / wikilinked notes manually (with mermaid / LaTeX rendering capability also) - but by using VS Code we have easy access to an Agent harness that we can direct to work on, or use our notes as context. Others have pointed out that context bloat is an issue, but no more so than when you use the copilot harness (or any other) inside a large codebase. I find I get more value from my AI conversations when I persist the outputs in markdown like this.

### cookiengineer (2026-04-05)

This is essentially the hack that Claude Code did with their dedicated sub-agent that summarizes a list of previous messages and maintains different types of memory (temporary, persistent, cross-agent). They also built tools so that agents can talk to each other and use that summary to propagate knowledge to other agents if needed.

Setting aside that their codebase is absolute slopcrap, I think something like this might work nicely if it's built from the ground up.

For my own test environment I'm relying on Golang and its conventions (go build, go test, go fmt, gopls etc) which saves a lot of prompts and tokens down the line. Additionally I think that spec driven development might be more successful but I haven't found out yet what the right amount of details for specifications is, so that semantic anchors can help summarize it better.

Anyways if you're curious, it's made for short agent lifecycles and it kinda works every time most of the time: https://github.com/cookiengineer/exocomp

Still need to implement the summarizing agent and memory parts, it's a little fiddlework to get that right so I'm currently experimenting a lot locally with both ollama and vllm as an inference engine.

### ansc (2026-04-05)

The comments in the gist is depressing.

### zbyforgotpass (2026-04-05)

A list of systems that implement this or related ideas: https://zby.github.io/commonplace/notes/related-systems/rela...

This list is also part of my own contender in this race: https://zby.github.io/commonplace/ - my own LLM operated knowledge base (this is the html rendering of that KB - there is also the github repo linked there).

The main feature is that I use it to build a theory about such systems - and the neat trick is that llms can read this theory and implement it so the very theory works as an LLM runtime too.

It works for me - but it has some rough edges still - so I guess it is not for everyone.

### sornaensis (2026-04-05)

I've been working on my own thing with more of a 'management' angle to it. It lets me connect memories to tasks and projects across all of my workspaces, and gives me a live SPA to view and edit everything, making controlling what the models are doing a lot easier in my experience https://github.com/Sornaensis/hmem in a way that suits how I think vs other project management or markdown systems.

I would be interested in trying to make the models go into more of a research mode and organize their knowledge inside it, but I've found this turns into something like LLM soup.

For coding projects, the best experience I have had is clear requirements and a lot of refinement followed through with well documented code and modules. And only a few big 'memories' to keep the overall vision in scope. Once I go beyond that, the impact goes down a lot, and the models seem to make more mistakes than I would expect.

### tristanMatthias (2026-04-05)

I built a tool[0] in a similar vein to this. It’s for the code base specifically, uses hashes to detect source file changes and distills everything down with an llm into a single asset that explains each file. All addressable via a cli.

I find it helps a LOT with discovery. Llm spends a lot less time figuring out where things are. It’s essentially “cached discovery”

[0] https://github.com/tristanmatthias/llmdoc

### thoughtpeddler (2026-04-05)

I’m surprised Karpathy thinks this is a viable solution for a quasi-continual learning system. Yes, it’s cool to experiment with these sort of ‘intermediate knowledge systems’, but the real goal within this current LLM paradigm remains clear: new information within a knowledge system should be manifest through updating the weights! Many efforts are taking a crack at this, which this really helpful talk [0] by Jack Morris goes into. I’m in full agreement with the other comments here: this ‘LLM Wiki’ merely results in “2nd-order information” that will only muddy the picture.

[0] “Stuffing Context is not Memory, Updating Weights Is": https://www.youtube.com/watch?v=Jty4s9-Jb78

### claudiug (2026-04-05)

i had the feeling that Karpathy is not a software developer at all, reading his tweets is like he is discovering half a century ago ideas. like specification...

### thegeolab (2026-04-06)

Built a versioned schema standard on top of this workflow: github.com/arturseo-geo/llm-knowledge-base

### vbarsoum (2026-04-06)

I built an implementation of this and tested it on 3 Alex Hormozi books (~155K words, 68 source files). Some data for the skeptics:
                                                                                                                                                                                              
  The naive version (each book as 1 file) produced exactly the slop people are describing here. But splitting into chapter-level files and recompiling changed the output categorically. Same model, same prompts — the only variable was source granularity.                                                                                                                             
                  
  The compiler produced 210 concept pages with 4,597 cross-references (19.2 avg links per page). 20+ concepts synthesized across all 3 books unprompted — one pulled from 11 source files and found a genuine contradiction between two books that neither makes explicit. 173K words of output from 155K input. It's not compression — it's synthesis.
                                                                                                                                                                                              
  The thing I think the "this is just RAG" comments are missing: a vector database is only useful to machines. You can't open a .faiss file and browse it. A wiki is useful to both. I open these files in Obsidian, browse the graph, follow links, read concept pages — no AI needed. But when I do ask the AI a question, it reads the same wiki pages I do, and the answers are better than RAG because the knowledge is already structured and cross-referenced instead of retrieved as raw chunks.                                                                        
                  
  That's the key insight in Karpathy's idea. The compiled wiki is the interface for humans AND the knowledge layer for AI. Same artifact, two audiences.                                      
   
  ~Cost: 12M tokens, ~10-15 min. Repo: https://github.com/vbarsoum1/llm-wiki-compiler

### asakin (2026-04-08)

I'd been working on a personal wiki system for a while and adapted it to implement this pattern. Ended up as a git template:

https://github.com/asakin/llm-context-base

Main additions on top of the pattern: a training period where the AI learns how you work over 30 days then gets quieter over time, a metadata standard so files are queryable by summary, and a lint pass for stale content and context loading optimization. Never have to design a taxonomy upfront.

### saberience (2026-04-10)

Karpathy is at his best when working on teaching materials for ML beginners.

When it comes to other stuff he seems to be in a X/Twitter induced AI Psychosis like Garry Tan where he thinks everything he does is amazing and novel because he gets glazed by 1000 X bots who just post "You're amazing" after anything he tweets.

This is most definitely an (old) solution in search of a problem. Plenty of people have been trying variants of this for several years at this point but the issue isn't putting stuff into a wiki, or git, or markdown files. It's how you keep then up to date, how you deal with conflicts, how you deal with bloat, how you decide what to keep and delete over time, and also, when you've got this big mass of notes and markdown, when do you surface it?

It sounds great on paper until you try and use it and realize that in reality it isn't that useful and doesn't become part of your daily life. That is, it's more fun to build than to actually use, and you don't end up using it outside of the initial novelty.
