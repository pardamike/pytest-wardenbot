# GitHub release notes — v0.1.0

Paste this into the GitHub release UI at
`https://github.com/pardamike/pytest-wardenbot/releases/new?tag=v0.1.0`.

Suggested release title: `v0.1.0 — Initial public release`

---

## What this is

A pytest plugin for testing chatbots and LLM apps. `pip install
pytest-wardenbot`, write a 5-line `chatbot` fixture pointing at your
bot, run `pytest`. **30 deterministic attacks** fire against your live
chatbot in under a second — prompt injection, system-prompt leak,
refusal bypass, indirect/XPIA, encoded payloads, multi-turn jailbreak.
Plus business-truth verification, optional LLM-judge tests, and an
opt-in canary-token leak detector.

Apache 2.0. No telemetry. Built so you can audit every prompt the suite
sends.

## Highlights

- **30 deterministic shipped tests** across 7 attack categories — zero
  LLM API spend by default.
- **Bundled vendor adapters** — `OpenAIChatAdapter`,
  `AnthropicMessagesAdapter`, `HTTPChatbotAdapter`. Sync + async pairs
  for each. `ChatbotAdapter` and `AsyncChatbotAdapter` Protocols if you
  need to wrap a custom RAG / agent stack.
- **Multi-turn attacks with real session memory** — when paired with a
  session-aware adapter, the multi-turn corpus chains priming turns
  before delivering the payload.
- **Canary-token leak detection** — plant a high-entropy token in your
  system prompt, suite asserts it never leaks. Strongest single signal
  for system-prompt disclosure.
- **Agent-ready failure messages** — each failure includes a Markdown
  remediation block you paste into Cursor or Claude Code.
- **Per-corpus override fixtures** — substitute or extend any attack
  corpus from your `conftest.py`.
- **`WardenBotInfraError`** distinguishes "bot is unreachable / malformed"
  (pytest ERROR) from "bot failed a check" (pytest FAILURE). Cleaner CI
  signal.
- **Response payload redaction** — `ChatbotResponse.raw` strips values
  with sensitive-looking keys (authorization, api-key, cookie) before
  storing, so test failures don't dump credentials to CI logs.
- **EVALUATION.md harness** — reproducible methodology for running the
  suite against named frontier bots; fill in the result tables yourself.

## What's deferred to v0.2

- Native-async shipped tests (parallel probe fan-out).
- Bundled LangChain / MCP adapters.
- Broader XPIA via RAMPART integration.
- Multi-judge ensemble mode for safety-critical scoring.

See `BUILD-PLAN.md` in the repo for the full roadmap.

## What "passing" means (and doesn't)

A green run means the bot didn't fail any of the 30 attacks in the most
overt way. Useful smoke test + regression detector. **It is not a
security audit.** Frontier-grade attacks are multi-turn, novel, and
adapted to your specific bot; no fixed corpus catches all of them.
Pair this with periodic red-team work for serious deployments. The
README and `docs/index.md` make this point explicit.

## Install

```bash
pip install pytest-wardenbot

# Or with optional extras:
pip install "pytest-wardenbot[judge]"        # DeepEval-backed LLM-judge checks
pip install "pytest-wardenbot[openai]"       # OpenAIChatAdapter + AsyncOpenAIChatAdapter
pip install "pytest-wardenbot[anthropic]"    # AnthropicMessagesAdapter + AsyncAnthropicMessagesAdapter
```

## Links

- **Docs:** https://pardamike.github.io/pytest-wardenbot/
- **Quickstart:** https://pardamike.github.io/pytest-wardenbot/quickstart/
- **Evaluation methodology:** https://pardamike.github.io/pytest-wardenbot/about/evaluation/

## Acknowledgments

The plugin is the OSS slice of WardenBot AI's Continuous Monitoring
service. The architecture decisions (script-first / LLM-fallback doctrine,
per-corpus override fixtures, agent-ready remediation messages) came out
of designing for the larger managed product first.

Pre-launch persona reviews from CTO, DevSecOps, Lead Developer, and
SMB CISO viewpoints shaped the final v0.1 scope — especially the move to
30 deterministic tests (up from 18 originally planned), the
`WardenBotInfraError` taxonomy, the `COMPLIANCE_MARKERS` false-positive
audit, and the canary-token detection layer.
