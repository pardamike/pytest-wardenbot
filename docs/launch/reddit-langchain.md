# /r/LangChain — Show post

**Suggested title:** `Built a pytest plugin for testing chatbots — works
with any LangChain stack, sync or async`

**Suggested flair:** `Tutorial` or `Resources`

---

Hey r/LangChain,

If you've shipped a LangChain-backed chatbot to production you've
probably hit the same wall I did: how do you actually test it?

Most options are awkward — roll your own per-prompt assertions
(50+ lines each), use a vendor CLI that doesn't integrate with your
existing test setup, or use research libraries like Garak that need an
afternoon of glue code before they catch anything.

I built `pytest-wardenbot` as the test-your-chatbot equivalent of
`pytest --cov`: drop in a plugin, get a curated suite, see what breaks.

**What it tests (out-of-the-box, 30 deterministic checks):**

- Prompt injection / jailbreak resistance
- System-prompt leak via direct asks + translation + repeat-above
- Refusal-bypass under roleplay / educational pretext / hypothetical
- Off-topic deflection
- Indirect prompt injection (XPIA) — directives embedded in retrieved
  documents, JSON fields, HTML comments. Especially relevant for RAG.
- Encoded-payload smuggling (Base64, ROT13, leet, hex)
- Multi-turn jailbreak (priming + payload) — handles session memory
- Canary-token leak detection (opt-in)

Plus user-parametrized:
- Business-truth verification (your bot's actual facts stay correct
  across model updates)
- Optional LLM-judge tests (brand voice, hallucination grounding,
  off-policy) via the `[judge]` extra

**Wiring to a LangChain chain:**

```python
# conftest.py
import pytest
from pytest_wardenbot.adapters.base import ChatbotResponse
from my_app.chain import build_my_rag_chain

class LangChainAdapter:
    name = "my-rag-chain"
    def __init__(self):
        self.chain = build_my_rag_chain()
        self.sessions = {}
    def send_message(self, prompt, *, session_id=None):
        history = self.sessions.setdefault(session_id, []) if session_id else []
        result = self.chain.invoke({"input": prompt, "history": history})
        if session_id:
            history.append(("user", prompt))
            history.append(("assistant", result["output"]))
        return ChatbotResponse(text=result["output"], raw=result)
    def reset_session(self, session_id):
        self.sessions.pop(session_id, None)

@pytest.fixture
def chatbot():
    return LangChainAdapter()
```

That's the whole adapter. ChatbotAdapter is a Protocol — just `name`,
`send_message`, `reset_session`. No inheritance, no plugin internals.

**Why pytest specifically:**

- Your existing tests already use it. Same runner, same CI integration.
- Failure messages are pytest assertions with structured payloads —
  prompt + response + matched indicators + a Markdown remediation block
  you can paste into Cursor or Claude Code.
- Deterministic checks first (zero LLM spend), LLM-judge is opt-in.
  Keeps CI cheap.

**Honest scoping:** a green run is a smoke test + regression detector,
not a security audit. Real adversarial attacks against deployed
chatbots are multi-turn, novel, and adapted to your specific
bot. No fixed corpus catches all of them. The README says so.

**Async:** for users running async LangChain code, both
`AsyncChatbotAdapter` Protocol and async vendor adapters
(`AsyncOpenAIChatAdapter`, `AsyncAnthropicMessagesAdapter`) ship in
v0.1. The shipped tests are sync but there's a `to_sync()` bridge so you
can use async adapters today.

**Links:**

- Code: https://github.com/pardamike/pytest-wardenbot
- Docs: https://pardamike.github.io/pytest-wardenbot/
- PyPI: https://pypi.org/project/pytest-wardenbot/

Apache 2.0. No telemetry. The plugin is the OSS slice of a managed
Continuous Monitoring service (scheduled runs, dashboards, alerts —
things a CI plugin alone can't provide). Intake form at
wardenbot.ai/intake/.

What I'd love to know:

- Are these the test categories you'd want for a LangChain RAG
  deployment? What's missing?
- Multi-turn handling — your RAG chain probably has its own session
  state model. Does the `session_id` Protocol parameter map cleanly to
  what you'd need?
- For agent stacks (LangGraph, etc.), what would XPIA tests need to
  look like to catch attacks via tool calls?
