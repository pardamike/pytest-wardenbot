# Show HN — title and body

## Suggested title

> Show HN: Pytest plugin for testing chatbots — jailbreaks, leaks, hallucinations

Alternates (pick the one that fits the moment):

- `Show HN: pytest-wardenbot – jailbreak/leak/hallucination tests for your chatbot`
- `Show HN: I added a chatbot to our product; this is what I wished pytest did`

## Body

Hi HN,

I'm Mike, founder of WardenBot AI. We're building a managed monitoring
service for AI chatbots — but the corpus of tests we run is more useful
than the dashboard layer for most people, so I packaged the corpus as an
Apache-2.0 pytest plugin and shipped it first.

**What it is:** `pip install pytest-wardenbot`. It ships 30 deterministic
tests against your chatbot: prompt injection, system-prompt leak, refusal
bypass, off-topic deflection, indirect/XPIA via embedded directives,
encoded-payload jailbreak (Base64/ROT13/leet/hex), multi-turn jailbreak
(priming + payload), and a canary-token leak detection helper. Optional
LLM-judge tests (DeepEval, brand voice + semantic equivalence + grounding)
ship via the `[judge]` extra.

**What "passing" actually means:** the bot didn't fail any of the 30
attacks in the most overt way. It's a smoke test + a regression detector
— if a deploy turns a green test red, that's real signal. It's not a
security audit. Adversarial prompts in the wild are multi-turn, novel,
and adapted to your specific bot; no fixed corpus catches all of them.
I'm pretty explicit about this in the README because I'd rather lose a
user than mislead one.

**Adapters:** bundled `HTTPChatbotAdapter` plus `OpenAIChatAdapter` (via
`[openai]`) and `AnthropicMessagesAdapter` (via `[anthropic]`). Both
sync + async. For chatbots with middleware (RAG, agents, tool calls),
write a ~20-line adapter implementing the `ChatbotAdapter` Protocol —
there's an example in the repo.

**Honest about the LLM-judge layer:** single-LLM judges agree with human
raters ~80% of the time per published research. The README says so;
failure messages cite it. v0.2 will add multi-judge ensemble for
safety-critical scoring.

**Funnel disclosure:** the OSS plugin is the top-of-funnel for our paid
Continuous Monitoring service (cross-run history, scheduled runs, alerts
— things you genuinely need a server for). The funnel is `wardenbot.ai/intake/`
in the README. No telemetry. No "buy now" CTAs. The plugin is useful
standalone.

**Differentiation:**

- vs Promptfoo (acquired by OpenAI Feb 2026): they're a CLI; we're a
  pytest plugin. Same test runner you already have, same CI integration.
- vs DeepEval: they're metrics-focused (faithfulness, relevancy); we're
  attack-focused (jailbreak, leak, bypass). We use DeepEval for our
  optional judge layer.
- vs Garak / PyRIT: they're research-grade attack libraries; we're a
  curated subset packaged as everyday pytest tests with structured
  failure messages.

**Failure messages include an agent-ready remediation block** you can
paste into Cursor or Claude Code. Each failure tells you the prompt sent,
the response, which patterns matched, and concrete suggestions for
strengthening the system prompt. Saves the "ugh, what do I do with this?"
step after a red CI.

**Links:**

- Code: https://github.com/pardamike/pytest-wardenbot
- Docs: https://pardamike.github.io/pytest-wardenbot/
- PyPI: https://pypi.org/project/pytest-wardenbot/

I'd love feedback on the corpus — what attacks should be in v0.2? What
patterns am I missing? Code review on the adapter Protocol or the
multi-turn session-state handling especially welcome.

Thanks for reading.
