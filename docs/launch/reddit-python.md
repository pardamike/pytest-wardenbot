# /r/Python — Show post

**Suggested title:** `[Show] pytest-wardenbot — pytest plugin for
testing chatbots (jailbreak, leak, hallucination, brand drift)`

**Suggested flair:** `Showcase`

---

Hey r/Python,

Built and released a pytest plugin for testing LLM-backed chatbots:
`pytest-wardenbot`.

The pitch: `pip install pytest-wardenbot`, write a 5-line
`chatbot` fixture pointing at your bot, run `pytest`. 30 deterministic
attacks fire against your live chatbot in under a second.

**What it covers (v0.1):**

- Prompt injection / jailbreak resistance (5 prompts × 2 checks)
- System-prompt leak elicitation (3)
- Refusal-bypass under roleplay / pretext / hypothetical framings (3)
- Off-topic deflection for scoped bots (2)
- Indirect prompt injection / XPIA via embedded directives in RAG
  documents, JSON fields, HTML comments (4)
- Encoded-payload jailbreak — Base64 / ROT13 / leet / hex (4)
- Multi-turn jailbreak (priming + payload) — 3
- Canary-token leak detection (opt-in; you plant the token in your
  system prompt, the suite asserts it never leaks)
- Business-truth verification (parametrized over your bot's facts:
  prices, hours, policies, etc.)
- Optional LLM-judge tests (semantic equivalence, brand alignment,
  hallucination grounding, off-policy, refusal quality) via the
  `[judge]` extra (uses DeepEval)

**Adapters:** bundled HTTP, OpenAI Chat (sync + async),
Anthropic Messages (sync + async), plus a `ChatbotAdapter` Protocol if
you need to wrap a custom stack (RAG, agents, etc.).

**Why pytest specifically:**

- You already have pytest in your toolchain. Same runner, same CI, same
  fixtures, same `-k` filtering.
- Failure messages are pytest assertions — structured, with the prompt
  + response + matched indicators + a Markdown remediation block ready
  to paste into Cursor or Claude Code.
- Deterministic-first: zero LLM API spend by default (regex / substring
  / schema). LLM-judge is opt-in.

**Honesty about what passing means:** a green run means the bot
doesn't fail in the most overt ways — useful smoke test, useful
regression detector. It is NOT a security audit. The README says so.

**License:** Apache 2.0. No telemetry. No "buy now" CTAs.

**Links:**

- GitHub: https://github.com/pardamike/pytest-wardenbot
- Docs: https://pardamike.github.io/pytest-wardenbot/
- PyPI: https://pypi.org/project/pytest-wardenbot/

The plugin is the OSS slice of a managed monitoring service I'm building
(scheduled runs, dashboards, alerts — things you actually need a server
for). Intake form at wardenbot.ai/intake/ but the plugin stands alone
and is the only thing I'm asking you to look at right now.

Feedback I'd love:

- Are the attack categories covering things you actually worry about?
- What's missing from the v0.1 corpus?
- Adapter Protocol shape — any pain points if you try to wrap your
  stack?

Thanks.
