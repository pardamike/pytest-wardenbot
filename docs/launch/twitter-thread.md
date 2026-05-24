# Twitter / X — launch thread

Each numbered tweet is one post. Keep each under 280 chars (Twitter)
or use the longer X version. Replace `[handle]` with your handle.

---

**1/**

Your chatbot has 30 attack tests it can't pass yet. Here are mine.

`pip install pytest-wardenbot`

Pytest plugin. Apache 2.0. Tests run against your live chatbot in under
a second. No LLM API spend.

🧵👇

---

**2/**

What's in the suite (v0.1, all deterministic):

- 5 jailbreaks × 2 checks
- 3 system-prompt leak elicitations
- 3 refusal-bypass framings
- 2 off-topic deflection
- 4 indirect injection (XPIA) via embedded directives
- 4 encoded-payload (Base64/ROT13/leet/hex)
- 3 multi-turn jailbreak

30 total.

---

**3/**

What it does NOT mean when it passes:

It does NOT mean your bot is secure.

It DOES mean your bot didn't fail in the most overt ways the public
corpus knows about. Smoke test + regression detector. Pair it with real
red-team work for serious deployments.

---

**4/**

The funnel category nobody talks about:

`business_truth_fact` fixture. Parametrize your bot's actual prices,
hours, policies, refund window. Suite asserts the bot answers them
correctly on every CI run.

This is the test that catches "we upgraded our model and now it says
the wrong price."

---

**5/**

Failure messages include a structured remediation block you can paste
into Cursor or Claude Code. Tells you:

- The prompt sent
- The bot's response
- Which detection patterns matched
- Concrete steps to fix it in your system prompt

Saves the "ok what now" step after a red CI.

---

**6/**

Bundled adapters:

- HTTPChatbotAdapter (sync + async)
- OpenAIChatAdapter (sync + async)
- AnthropicMessagesAdapter (sync + async)

For RAG/agent stacks: implement the `ChatbotAdapter` Protocol — three
members, ~20 lines.

---

**7/**

Why pytest specifically:

You already have it.

Same CI integration. Same `-k` filtering. Same fixtures. Same `--strict-markers`.
Failure output is pytest assertions — structured, debuggable, paste-able
to your AI dev tool of choice.

---

**8/**

Honesty notes:

- Apache 2.0 forever
- No telemetry
- The plugin is the OSS slice of a managed monitoring service I'm
  building. Intake form at wardenbot.ai/intake/. Plugin stands alone.

---

**9/**

Code: https://github.com/pardamike/pytest-wardenbot
Docs: https://pardamike.github.io/pytest-wardenbot/
PyPI: https://pypi.org/project/pytest-wardenbot/

What's missing from the v0.1 corpus? What attacks should be in v0.2?
PRs welcome.
