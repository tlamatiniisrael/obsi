
![](https://miro.medium.com/v2/resize:fit:700/1*LGHRO7wmg6R0L_ja1ZOUzg.png)

## Everyone assumes Claude Code needs an Anthropic subscription. They’re wrong.

I was about to enter my credit card for the Anthropic Pro plan.

$20/month just to use Claude Code felt excessive. I’d already spent $60 on Cursor. Another $20 for what looked like the same thing?

Then I checked the docs one more time.

**Claude Code doesn’t require Anthropic API.**

## The Assumption Everyone Makes

March 2026. Claude Code launches as a CLI tool.

Everyone on Twitter: “Just signed up for Anthropic Pro to use Claude Code!”

I almost did the same.

Downloaded Claude Code. Ran `claude-code init`. Got the API key prompt.

**Assumed I needed Anthropic. Everyone else was paying. Why wouldn’t I?**

Then I noticed something in the configuration file:

```
{  
  "apiProvider": "anthropic",  
  "model": "claude-sonnet-4"  
}
```

Wait. If `apiProvider` is a configurable field...

**What other providers does it support?**

## The Discovery

I checked the Claude Code GitHub issues. Buried in comment #47 of a feature request thread:

_“You can use Ollama. Just change the provider config.”_

No fanfare. No announcement. Just a random comment.

I tried it:

```
claude-code config set apiProvider ollama  
claude-code config set model qwen2.5-coder:32b
```

Started Claude Code.

**It worked.**

No Anthropic API key. No $20/month subscription. No rate limits.

Just local inference on my M2 MacBook.

## Why This Actually Works

Claude Code is not “Claude.”

**It’s a harness.**

The actual components:

- Terminal interface
- File system access
- Command execution
- Model communication layer

The model itself? Completely swappable.

Anthropic built it to work with their API. But the architecture is provider-agnostic.

You can plug in:

- Ollama (local models)
- LM Studio (local models)
- OpenRouter (cheaper API access)
- llama.cpp (if you’re hardcore)

**Claude Code doesn’t care. It just needs an LLM that can follow instructions.**

## The Performance Reality Check

Here’s where it gets interesting.

I tested the same refactoring task on three setups:

**Setup 1: Claude Sonnet 4 (Anthropic API)**

- Cost: $0.47 for the task
- Time: 8 seconds
- Quality: Perfect. Understood context immediately.

**Setup 2: Qwen 2.5 Coder 32B (Ollama local)**

- Cost: $0.00
- Time: 43 seconds
- Quality: Good. Missed one edge case.

**Setup 3: DeepSeek Coder 33B (OpenRouter)**

- Cost: $0.03
- Time: 12 seconds
- Quality: Good. Slightly verbose code.

**The gap isn’t as big as you’d think.**

For simple refactors, boilerplate generation, and code explanations? Local models are 90% as good.

For complex architecture decisions and production debugging? Claude Sonnet still wins.

## What Nobody Tells You About Local Models

**The Good:**

No rate limits. Run 1,000 requests if you want.

No API costs. Burn through tokens without worrying about the bill.

Privacy. Your codebase never leaves your machine.

**The Bad:**

Slower. 5–10x slower than API calls.

RAM hungry. 32B models need 24GB+ RAM.

Model switching pain. Each model has quirks you need to learn.

**The Ugly:**

You become your own DevOps. Model downloads, version management, performance tuning.

Context window limitations. Local models max out at 32K tokens. Claude Sonnet handles 200K.

## The Strategy I Actually Use Now

I don’t use one approach. I use three.

**For quick tasks (tests, docstrings, simple refactors):** → Qwen 2.5 Coder 32B (Ollama, free)

**For complex logic (architecture, debugging, optimization):** → Claude Sonnet 4 via OpenRouter ($0.003/1K tokens vs. Anthropic’s $0.015)

**For production-critical code reviews:** → Claude Opus 4 (Anthropic API, full price, because I’m not fucking around)

**Total monthly cost: $8.**

Anthropic Pro would be $20. Cursor is $20. GitHub Copilot is $10.

**I’m spending less and getting more flexibility.**

## The Setup (Actual Commands)

Install Ollama:

```
curl -fsSL https://ollama.com/install.sh | sh  
ollama pull qwen2.5-coder:32b
```

Configure Claude Code:

```
claude-code config set apiProvider ollama  
claude-code config set model qwen2.5-coder:32b  
claude-code config set baseUrl http://localhost:11434
```

Test it:

```
claude-code "refactor this function to use streams"
```

**Done. No credit card. No subscription.**

For OpenRouter (if you want better models for cheap):

```
claude-code config set apiProvider openrouter  
claude-code config set apiKey YOUR_OPENROUTER_KEY  
claude-code config set model deepseek/deepseek-coder
```

**OpenRouter has 100+ models. Most are cheaper than Anthropic.**

## The Catch (There’s Always a Catch)

Local models are not Claude.

They’re good. Sometimes very good. But they’re not Claude.

**Where local models struggle:**

Complex architecture decisions. They suggest patterns without understanding constraints.

Production debugging. They miss subtle concurrency issues or race conditions.

Ambiguous requirements. They guess instead of asking clarifying questions.

**Where local models shine:**

Boilerplate code. They’re actually better at repetitive patterns.

Test generation. They nail the happy path every time.

Documentation. They’re surprisingly good at explaining code.

**The rule: Use local for volume. Use Claude for value.**

## What Most Developers Are Doing Wrong

**Mistake 1: Paying for everything**

You don’t need Anthropic Pro + Cursor Pro + Copilot.

Pick one premium tool. Use free/local for the rest.

**Mistake 2: Using one model for everything**

Claude Sonnet is overkill for writing tests.

Qwen Coder is insufficient for architecture reviews.

**Match the model to the task.**

**Mistake 3: Not testing local models**

“Local models are worse.”

Have you tried? Or are you repeating what you heard on Twitter?

I tested. For 60% of my tasks, local models are fine.

**Mistake 4: Assuming Claude Code = Claude**

Claude Code is a terminal interface.

The model powering it is configurable.

**You’re paying for the brand, not the product.**

## The Uncomfortable Economics

Anthropic wants you to think Claude Code requires their API.

**It doesn’t.**

They want you to pay $20/month for Pro.

**You don’t have to.**

The open-source community built models that are 90% as good for $0.

**But nobody’s promoting them because there’s no business model.**

Anthropic has marketing budget. Ollama has GitHub stars.

**Guess which one most developers know about?**

## The Real Reason I Switched

It’s not just about saving $20/month.

**It’s about control.**

With Anthropic API:

- They control rate limits
- They control model availability
- They control pricing
- They can deprecate models whenever

With local models:

- I control everything
- No rate limits
- No surprise price increases
- Models work forever

**When you depend on one vendor, you’re at their mercy.**

I learned this when OpenAI doubled GPT-4 API pricing overnight.

**Not making that mistake again.**

## Should You Do This?

If you’re a hobbyist: **Yes. Absolutely.**

No reason to pay $20/month for weekend projects.

If you’re at a startup: **Maybe.**

Depends on your burn rate vs. engineering time value.

If you’re at a big company: **Probably not.**

Just expense Anthropic Pro. Your time is worth more than $20/month.

**But at least know you have options.**

Most developers don’t even know local models exist.

They see “Claude Code” and assume “must pay Anthropic.”

**That’s exactly what Anthropic wants you to think.**

## The Setup I’m Using Today

**Hardware:** M2 MacBook Pro, 32GB RAM

**Models:**

- Qwen 2.5 Coder 32B (via Ollama) — 80% of tasks
- DeepSeek Coder 33B (via OpenRouter) — 15% of tasks
- Claude Sonnet 4 (via Anthropic) — 5% of critical tasks

**Monthly cost:** $6–8 (vs. $20+ everyone else is paying)

**Productivity:** Same or better (because I’m not worrying about API costs)

## The Truth Nobody Wants to Say

You don’t need to pay Anthropic to use Claude Code.

You don’t need Cursor Pro for AI coding.

You don’t need GitHub Copilot for autocomplete.

**You need to understand what these tools actually are.**

Most of them are just wrappers around LLM APIs.

And most LLM APIs are interchangeable.

**The expensive part is the compute. Not the interface.**

Run the compute yourself, and the interface is free.

**I spent $240 on AI tools in 2025 before I figured this out.**

**Now I spend less than $100/year and get better results.**

Not because I’m smarter. Because I read the docs instead of the marketing.

## Learn What Actually Works in Production

I figured this out after wasting $240 on tools I didn’t need.

**🤖 AI for Production Engineers**  
The real cost breakdown of AI coding tools. Which ones are worth paying for, which ones have free alternatives.  
→ [Get it here](https://devrimozcay.gumroad.com/l/mkhsj)

**📦 Production Engineering Starter Pack**  
Includes the model comparison spreadsheet I use to pick the right tool for each task.

[

## Production Engineering Starter Pack - The "Stop Firefighting" Kit

### 🚨 Stop Learning Production the Hard WayYou don't rise to the level of your knowledge in production.You fall to the…

devrimozcay.gumroad.com



](https://devrimozcay.gumroad.com/l/szjwly?source=post_page-----bd4669f5d038---------------------------------------)

**📬 Weekly Production Insights**  
Real tools, real costs, real alternatives. Join 2,400+ engineers who stopped overpaying for SaaS.  
→ [Subscribe on Substack](https://substack.com/@devrimozcay1)

**🚨 Built After Wasting Money on Tools**  
I built **ProdRescue AI** after realizing most incident tools are overpriced wrappers. Logs → RCA in 2 minutes, no enterprise markup.  
→ [Try it free](https://www.prodrescueai.com/)

**Still paying $20/month for Claude Code?** Drop your setup in the comments. Let’s compare notes.

_Thank you for reading. If this saves you $240/year, buy me a coffee. Or don’t. I’m not your mom._

[

Programming

](https://medium.com/tag/programming?source=post_page-----bd4669f5d038---------------------------------------)

[

Web Development

](https://medium.com/tag/web-development?source=post_page-----bd4669f5d038---------------------------------------)

[

AI

](https://medium.com/tag/ai?source=post_page-----bd4669f5d038---------------------------------------)

[

LLM

](https://medium.com/tag/llm?source=post_page-----bd4669f5d038---------------------------------------)

[

Data Science

](https://medium.com/tag/data-science?source=post_page-----bd4669f5d038---------------------------------------)

46

1

[

![Engineering Playbook](https://miro.medium.com/v2/resize:fill:96:96/1*zACrMGYAWryjYXgJs_52mw.png)



](https://medium.com/engineering-playbook?source=post_page---post_publication_info--bd4669f5d038---------------------------------------)

[

## Published in Engineering Playbook

](https://medium.com/engineering-playbook?source=post_page---post_publication_info--bd4669f5d038---------------------------------------)

[454 followers](https://medium.com/engineering-playbook/followers?source=post_page---post_publication_info--bd4669f5d038---------------------------------------)

·[Last published May 4, 2026](https://medium.com/engineering-playbook/python-datetime-part-2-formatting-parsing-and-unix-timestamps-3521a3f5a091?source=post_page---post_publication_info--bd4669f5d038---------------------------------------)

We write about software engineering, backend architecture, DevOps, cloud systems, AI engineering, system design, performance tuning, debugging, production failures, and the lessons learned the hard way. If you’re building something real — this is your playbook.

Follow

[

![Root Cause](https://miro.medium.com/v2/resize:fill:96:96/1*iROP_E8YWwxoo7W9WVcDoQ.png)



](https://medium.com/@coding_with_tech?source=post_page---post_author_info--bd4669f5d038---------------------------------------)

[

## Written by Root Cause

](https://medium.com/@coding_with_tech?source=post_page---post_author_info--bd4669f5d038---------------------------------------)

[864 followers](https://medium.com/@coding_with_tech/followers?source=post_page---post_author_info--bd4669f5d038---------------------------------------)

·[137 following](https://medium.com/@coding_with_tech/following?source=post_page---post_author_info--bd4669f5d038---------------------------------------)

Debugging real production failures step-by-step. Find the issue before it finds you.

Follow