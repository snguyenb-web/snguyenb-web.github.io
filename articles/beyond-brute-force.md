---
title: "Beyond Brute Force: Treating LLM Inference Like an Operating System"
description: "I’m not an AI engineer. I’ve spent 25+ years designing chips, firmware, and embedded systems, where memory is always limited and every byte has to earn its…"
date: 2026-10-26
image: /articles/beyond-brute-force-cover.jpg
---

# Beyond Brute Force: Treating LLM Inference Like an Operating System

![Cover](beyond-brute-force-cover.jpg)

*By Simon Nguyen · Originally published on [LinkedIn](https://www.linkedin.com/pulse/beyond-brute-force-treating-llm-inference-like-operating-simon-nguyen-g6qkc/)*

Simon Nguyen · Patent pending · October 2026

### An outsider’s view

I’m not an AI engineer. I’ve spent 25+ years designing chips, firmware, and embedded systems, where memory is always limited and every byte has to earn its place. Recently I looked at how large language models work for the first time, and I saw them through those eyes.

What struck me was how much hardware they assume. In embedded design, you size the software to fit the memory you have. In AI today, the trend runs the other way: the models grow, and the memory has to grow with them.

So this article is an outsider’s view: a systems architect asking whether ideas from the embedded world (working sets, prefetching, fixed memory budgets) could make AI leaner. I may be missing things that people inside the field know well, and I’d welcome their corrections.

### The problem: a library that must fit on a desk

An AI model like ChatGPT or Claude runs on special processors called GPUs. Each GPU has a small amount of very fast memory right next to it, called HBM. Think of HBM as the desk where the worker sits: small, but everything on it can be reached instantly.

Today, the whole library has to fit on that desk. A large model keeps all of its knowledge in fast memory, and reads through all of it for every word it writes, whether you ask about heart surgery or tax law. Each question needs only one shelf, yet the entire library sits on the desk.

Newer designs called Mixture-of-Experts (MoE) help: they read only a few “experts” for each word, which saves work. But every expert must still stay on the desk, because the next word might need any of them. MoE saves reading time, but not desk space.

My idea is simple: bring only the right shelf to the desk, and pick it before the worker sits down.

### Why desk space matters to everyone

This is no longer only an AI-company problem. Memory makers have shifted factory capacity toward HBM for AI data centers, and every wafer used for HBM is one not used for the memory in phones and PCs. HBM also takes roughly three times the wafer area per bit of ordinary memory. Industry analysts expect memory supply to grow more slowly than usual through 2026, and PC makers have signaled price increases of 15–20%.

In the long run, pushing the limits drives volume and lowers cost per bit. In the short run, factories can’t grow fast enough, so AI’s appetite raises prices for everyone else. **Every gigabyte of HBM an AI model doesn’t need is memory that can go into a phone, a car, or a hospital device.**

### How an AI “knows” things: static and dynamic knowledge

An AI model learns by reading a huge amount of text once, during training. What it learns is stored inside the model itself, a bit like what a person remembers from school. That’s **static knowledge**: math, physics, grammar, history, how an engine works. It doesn’t change, so the model can answer from memory instantly, at no extra cost.

But the model’s memory is frozen on the day training ends. Anything that changes after that (prices, news, laws, who runs a company, a new research result) is **dynamic knowledge**. For that, the AI has to look it up and read the current information before it answers, just as you’d check today’s news rather than trust what you remember from last year.

I see this every time I work with an AI assistant on a design. When I ask how a memory bus works, it answers right away, because that’s static knowledge it learned in training. When I raised today’s memory-chip shortage, it searched first, because prices and supply change month to month and its training memory could be out of date.

Both paths have a cost:

• **Static** is fast and cheap to use, but expensive to update. Correcting it means retraining the model.

• **Dynamic** is cheap to update, but costs time and memory on every question, because the AI has to read the new information each time.

A well-designed system knows which kind of question it’s facing. Static questions go to a compact model that answers from memory. Dynamic questions go to a path that looks things up and is refreshed often. Getting this wrong in either direction is costly: looking up what you already know wastes time, and answering a dynamic question from old memory gives a confident wrong answer.

### The idea: a pre-LLM layer that decides before any model runs

Today’s systems start the big model first and decide later. My idea turns that around: put a small **pre-LLM layer** on top of the models, running on the server’s ordinary CPU. It decides how each request is handled **before** any model runs: is a model needed at all, which part of the knowledge is needed, which single processor must wake up, and does the answer need current information?

Other systems already use a router to pick a model. The pre-LLM layer does more: it also controls power, memory and correctness. It works the way an operating system decides which programs stay in memory and which processor runs them.

### The pre-LLM layer – on the CPU, before any GPU wakes up

•      A tiny classifier (~100M parameters, stored at 2 bits per value) runs on the server’s ordinary CPU. At ~25 MB, it can live inside the CPU’s own cache.

•      It sorts each request by subject, by static or dynamic knowledge, and by user level.

•      If a question needs current knowledge, it sends it to the fresh path, whatever the user’s plan.

•      Cached or trivial requests never touch a GPU.

•      For everything else, it starts loading the right part of the model and waking the target GPU while the request is still waiting in line.

### Subject routing = memory placement

•      The question goes to a subject group (medical, finance, engineering…). This choice is made once per question, not once per word.

•      Only a shared core plus the active subject group need to be in HBM. Everything else waits in slower, cheaper memory.

•      In a data center, each GPU server hosts one subject, and users are routed to the matching server, the way an operating system keeps related jobs on the same processor.

### A partitioning tool sizes each piece to the hardware

How finely should the knowledge be split? Instead of picking a fixed number of levels, a **partitioning tool** splits each subject until its piece fits a known hardware size, for example one 8-GPU server. It works like a linker script in embedded design: you declare the memory regions, and the tool places the sections to fit.

• **Input:** the target hardware size, a memory budget for active conversations, and the subject tree.

• **Rule:** keep splitting a branch until its shared core, its own experts, and its conversation memory fit the target.

• **Output:** a tree whose depth differs by branch. Medical might need three levels to fit, while history fits in one.

The tool must also stop splitting when a piece would be too narrow to train well, or when users mix two subjects so often that swapping between them would eat the savings. When the hardware changes, you rerun the tool and get a new layout, with no redesign. Inside each piece, standard MoE still handles the per-word work.

The industry scaled up because hardware allowed it. **This design asks what’s possible when you size the model to the memory, instead of the memory to the model.**

### Matching each piece to the right processor

GPUs aren’t the only AI processors. TPUs handle large, steady workloads efficiently, and NPUs (small, low-power AI chips found in phones and laptops) can answer simple questions using a fraction of the power. Once knowledge is split into pieces of known size, each piece can go to the processor that suits it best:

• **Static subjects** (math, history, language) rarely change and work well at low precision, so they suit **NPUs**.

• **Large, steady workloads** suit **TPUs**.

• **Dynamic subjects** need heavier reasoning and frequent updates, so they suit **GPUs**.

The pre-LLM layer becomes the dispatcher. It looks up where each piece lives, wakes **only** the processor that’s needed, and starts loading the data. Everything else stays idle. Like clock control in a power-efficient chip, nothing runs unless it has work to do.

The hardware side of this, a flat data server where the CPU, GPUs, TPUs and NPUs share one optical fabric, is described in my earlier article: [Breaking the Infrastructure Wall: Why AI Needs an Open, Modular, Optical-First Server](https://www.linkedin.com/pulse/breaking-infrastructure-wall-modular-opticalfirst-server-simon-nguyen-somzc/).

### Freshness is a correctness rule, not a pricing tier

Users differ: free vs. paid, patient vs. clinician. Tier decides resources (speed, depth). Expertise decides how the answer is presented.

But one rule sits above both: **if a question needs current knowledge, it gets current knowledge, whatever the user’s plan.** Serving a stale answer to save cost isn’t an optimization. It’s a bug, and in medicine or finance, a dangerous one.

### What could it save? (Analytical estimate, not measured)

Under stated assumptions (340B total parameters, 8 subject groups, ~30B active per word, current high-end GPU servers):

• **HBM:** roughly 63–74% less than a standard MoE model with all experts kept in fast memory

• **Speed:** about the same as that MoE model (0.9–1.0×), minus a ~1.25 s cost when switching subjects, which the pre-LLM layer partly hides by loading early

• **Versus a conventional model of the same size:** roughly 10× less data read per word

The savings shrink as conversations get longer, and serving many users at once only works when each server hosts its own subject. These numbers need real measurement.

For a company serving AI at scale, that means more users per server, lower cost per answer, and new customers who couldn’t afford a cluster before.

### Private AI on a single server

Many of today’s largest open models don’t fit on one 8-GPU server, even before any user is served. They need clusters of servers with fast links between them, which only large cloud providers can afford.

If each subject group is sized to fit one server, an organization can run just the subjects it needs. **A hospital could run a medical-only model on a single on-site server, keeping patient data in-house.** That turns a memory optimization into something more important: private AI for organizations that can’t afford a cluster.

### It also makes engineering teams more focused

• **Clear ownership:** one team owns “Medical-Static,” another “Finance-Dynamic,” each with its own data, tests, and release schedule.

• **Smaller blast radius:** a bad finance update can’t break medical answers.

• **Faster iteration:** retraining one subject group takes far less time and money than retraining one giant model.

• **Easier debugging:** when an answer is wrong, you know which part produced it.

The router and the shared core still need a central owner and strict change control, since every team depends on them. This is the same lesson software learned moving from monoliths to modules.

### Standing on others’ work

This builds on Mixture-of-Experts, SambaNova’s Composition of Experts, Meta’s Branch-Train-MiX, expert-offloading research, and prefix caching. What I’m proposing is the combination: routing by subject and freshness at every level, driving both memory placement and update policy; a partitioning tool that sizes each piece to the hardware; freshness enforced as correctness; and, at the center of it all, a pre-LLM layer on the CPU that decides before any model runs and wakes only the processor that’s needed.

### Closing thought

This isn’t an argument against anyone. My goal is AI that is faster, uses less power, and costs less for everyone: the companies that build it, the people who use it, and the organizations that can’t afford it today.

People keep learning throughout their lives. Many of us bring deep skills from one field and keep picking up new ones as we grow older. AI can be a great partner in that, but only if it is quick, affordable, and smart about what it knows and what it needs to look up.

AI should make human life simpler and better, with people staying in control. Making AI efficient is part of that. Cheaper, leaner inference means these tools can reach more people, including in medicine, where both freshness and trust matter most.

I’d welcome feedback from people working on AI infrastructure: where does this break?

*Simon Nguyen, Systems Architect*

Patent pending. © 2026 Simon Ba Nguyen.

### Glossary

• **LLM (large language model):** An AI system, such as ChatGPT or Claude, that reads and writes text.

• **GPU:** The processor that runs AI models; very fast at doing many calculations at once.

• **TPU:** A processor built by Google specifically for AI; efficient on large, steady workloads.

• **NPU:** A small, low-power AI processor, found in phones, laptops, and other devices.

• **HBM (high-bandwidth memory):** The small, very fast memory stacked next to a GPU. The “desk” in this article.

• **Parameters / weights:** The numbers inside a model that hold what it learned. 340B means 340 billion of them.

• **Token:** A word or piece of a word; models write one token at a time.

• **MoE (Mixture-of-Experts):** A model design where only a few sub-networks (“experts”) work on each token.

• **KV cache:** The model’s short-term memory of the current conversation; it grows as the conversation gets longer.

• **Quantization (2-bit, 4-bit):** Storing each number with fewer bits to save memory, at some cost in accuracy.

• **Prefetch:** Loading data before it’s needed, so it’s ready when the work starts.

### Sources

• [Global Memory Shortage Crisis: IDC’s Market Analysis (EMSNow)](https://www.emsnow.com/?p=52574)

• [2026 AI boom memory chip shortage (Bloomberg)](https://www.bloomberg.com/graphics/2026-ai-boom-memory-chip-shortage/)

• [Chip industry facing the great memory pivot (EE Asia)](https://www.eetasia.com/chip-industry-facing-the-great-memory-pivot/)
