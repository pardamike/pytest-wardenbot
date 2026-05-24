# pytest-wardenbot — Build Plan

The plan for shipping `pytest-wardenbot` v0.1.0 to PyPI. ~7 sessions, each roughly half-day to full-day of focused work. Pulled forward from Wave 5 of the main WardenBot AI roadmap (see `~/Code/wardenbot/docs/implementation/14-execution-plan.md` §W5.13–W5.14) so the OSS funnel can seed dev mindshare before the managed Continuous Monitoring SaaS launches.

## Context

**What this is:** An open-source pytest plugin (Apache 2.0) that ships curated tests for chatbots and LLM apps. Developers `pip install pytest-wardenbot`, point it at their chatbot, run `pytest`, get back pass/fail with agent-ready remediation prompts for any failures.

**Why now:** Three reasons.

1. The plugin is genuinely standalone — no backend, no Stripe, no infra. It can ship before the managed SaaS does.
2. Promptfoo (now OpenAI) and Giskard are racing toward our segment. A live PyPI package with stars and momentum plants a competitive flag.
3. The attack corpus is something we need anyway. Building the plugin first means the corpus gets curated and battle-tested by real users before paid customers depend on it.

**What this is NOT:**
- Not the managed SaaS (that's Wave 6 of the main roadmap).
- Not a sales tool with a "buy now" CTA (until the SaaS exists, the CTA is "join the waitlist").
- Not RAMPART-based (see §RAMPART below).
- Not a way to call our hosted API (Sentry-tier customers get that via a separate authenticated mode in v0.3+).

**Commitments we're making:**
- Apache 2.0 license, forever.
- Semver. No breaking changes within a major version.
- No telemetry by default. Ever.
- No bait-and-switch CTAs. The plugin's value proposition stands alone.
- ~10–20% of one engineer's time ongoing — the plugin is a marketing budget line, not a revenue line.

---

## RAMPART decision: defer to v0.2

The user asked specifically about including RAMPART. Decision: **defer to v0.2**. Reasoning:

| Concern | Detail |
|---|---|
| **Alpha software** | RAMPART v0.1.0 released 2026-05-19. Microsoft AI Red Team, 4 maintainers. APIs explicitly subject to change. Pinning to an alpha dependency in a *stable* OSS release of our own would be a chronic source of breakage. |
| **Agentic-only relevance** | RAMPART models XPIA (Cross-Prompt Injection Attack) — planting malicious instructions in data an agent reads, then triggering. The verdict logic returns UNDETERMINED for pure chatbots without tool calls. Our v0.1 users mostly have pure chatbots. |
| **Hard dependency conflict** | RAMPART pins `pyrit==0.13.0`. That collides with anyone else holding PyRIT. Bad neighbor for a general-purpose pytest plugin. |
| **No built-in chatbot adapters** | RAMPART ships zero adapters for OpenAI / Anthropic / LangChain / MCP / HTTP. We'd write them ourselves. Worth doing, but not before v0.1 ships. |

**v0.2 path:** ship RAMPART as an optional extras install — `pip install pytest-wardenbot[agentic]` — gated to users who declare their bot has tools. The `AttackRunner` adapter pattern in v0.1 makes this drop-in (we just register a `RampartAttackRunner` alongside the deterministic / DeepEval-judge runners).

Architecture decision: **v0.1 ships the adapter abstraction even though only one or two runners use it.** The cost is a Protocol class and a registry; the benefit is RAMPART (and any future runners) drop in without refactor.

---

## v0.1 scope

**Tests we ship in v0.1** (target: 15 deterministic + 5 optional LLM-judge):

| Category | Count | Grading | Requires API key? |
|---|---|---|---|
| Prompt-injection probes (regex on bad output) | 5 | deterministic | no |
| System-prompt leak elicitation (substring on canary) | 3 | deterministic | no |
| Refusal-bypass patterns (regex on disallowed-content tokens) | 3 | deterministic | no |
| Business-truth assertion helpers (numeric / exact / substring match) | 2 (helpers; user-configured) | deterministic | no |
| Off-topic deflection (substring on refusal markers) | 2 | deterministic | no |
| Optional: semantic-equivalence checks via DeepEval | 5 | LLM judge | user's API key |

The 15-deterministic-test floor matters: a new user runs `pytest` and sees 15 tests run in under 30 seconds against their bot with zero API spend. That's the install-to-first-green-test path that converts.

**Chatbot adapters in v0.1:**
- `HTTPChatbotAdapter` — generic HTTP POST with configurable auth (bearer / api-key header / custom header)
- `OpenAIChatAdapter` — OpenAI Chat Completions API (optional extra: `pip install pytest-wardenbot[openai]`)
- `AnthropicMessagesAdapter` — Anthropic Messages API (optional extra: `pip install pytest-wardenbot[anthropic]`)

LangChain, MCP, OpenAI Assistants, Slack/Teams/Discord adapters: v0.2+.

**Tech stack:**
- Python 3.11+ (matches RAMPART; modern enough for `Protocol` + `Self` types).
- `pytest >= 8` as the core runtime.
- `pydantic >= 2` for adapter / response models.
- `httpx` for HTTP transport.
- `pytest-asyncio` for async-fixture tests.
- Optional: `openai`, `anthropic`, `deepeval` — extras only.

**Package structure:**

```
pytest-wardenbot/
├── README.md
├── LICENSE.md                  # Apache 2.0 (already in place)
├── BUILD-PLAN.md               # this doc
├── pyproject.toml
├── .gitignore
├── .github/workflows/ci.yml
├── src/pytest_wardenbot/
│   ├── __init__.py
│   ├── plugin.py               # pytest plugin entry point + fixtures
│   ├── adapters/
│   │   ├── __init__.py
│   │   ├── base.py             # ChatbotAdapter Protocol
│   │   ├── http.py             # generic HTTP adapter
│   │   ├── openai_chat.py      # optional
│   │   └── anthropic_msgs.py   # optional
│   ├── runners/
│   │   ├── __init__.py
│   │   └── base.py             # AttackRunner Protocol (for RAMPART hook in v0.2)
│   ├── tests/                  # the shipped tests
│   │   ├── __init__.py
│   │   ├── test_prompt_injection.py
│   │   ├── test_system_prompt_leak.py
│   │   ├── test_refusal_bypass.py
│   │   ├── test_business_truth.py
│   │   ├── test_off_topic.py
│   │   └── test_semantic.py    # optional, requires [judge] extra
│   ├── grading/
│   │   ├── __init__.py
│   │   ├── deterministic.py
│   │   └── judge.py            # optional, requires [judge] extra
│   ├── corpus/                 # canonical attack strings
│   │   ├── __init__.py
│   │   ├── jailbreak.py
│   │   ├── leak_elicitation.py
│   │   └── refusal_patterns.py
│   └── remediation.py          # agent-ready Markdown formatter
├── tests/                       # tests OF the plugin itself
│   ├── conftest.py
│   ├── test_adapters.py
│   ├── test_grading.py
│   ├── test_plugin.py
│   └── fixtures/
│       └── mock_chatbot.py
├── examples/
│   ├── README.md
│   ├── basic_http.py
│   ├── openai_assistant.py
│   └── github_actions.yml
└── docs/
    └── (deferred to session 6 — mkdocs-material)
```

---

## Session breakdown

Each session is sized for ~half-day to full-day of focused work. Doable across 2–3 weekends or 2 weeks of evening sessions.

### Session 1 — Scaffold + first working test (today)

**Scope:** Get to a state where `pip install -e .` works AND `pytest` discovers and runs at least one wardenbot test against a mock chatbot.

**Deliverables:**
- `pyproject.toml` with metadata, deps, extras
- `.gitignore` for Python projects
- `src/pytest_wardenbot/__init__.py` + version
- `src/pytest_wardenbot/adapters/base.py` — `ChatbotAdapter` Protocol
- `src/pytest_wardenbot/adapters/http.py` — `HTTPChatbotAdapter`
- `src/pytest_wardenbot/plugin.py` — pytest plugin entry point with a `chatbot` fixture
- `src/pytest_wardenbot/tests/test_prompt_injection.py` — ONE deterministic prompt-injection test
- `src/pytest_wardenbot/corpus/jailbreak.py` — small starter corpus (5 entries)
- `src/pytest_wardenbot/grading/deterministic.py` — `assert_no_harmful_content` helper
- `src/pytest_wardenbot/remediation.py` — basic Markdown formatter
- `tests/fixtures/mock_chatbot.py` — in-memory chatbot for testing the plugin itself
- `tests/test_plugin.py` — at least one assertion that the plugin loads + the test runs
- `.github/workflows/ci.yml` — lint + test on push
- README slightly improved (still minimal; full marketing wait until launch)

**DoD:**
- `pip install -e .` succeeds locally
- `pytest tests/` passes (plugin's own tests)
- A user can write a `conftest.py` that registers an `HTTPChatbotAdapter`, then `pytest --pyargs pytest_wardenbot.tests` runs the shipped test against it
- CI passes on push

### Session 2 — Fill out the deterministic test corpus

**Scope:** Author the remaining 14 deterministic tests + their corpus entries.

**Deliverables:**
- 5 prompt-injection variants (jailbreak fragments, "ignore previous instructions" variants, prompt-leak triggers, payload smuggling)
- 3 system-prompt leak elicitation tests
- 3 refusal-bypass tests (role-play, "for educational purposes", DAN-style)
- 2 business-truth helpers (`assert_truth_fact_match`, supports exact / substring / numeric)
- 2 off-topic deflection tests
- Each test ships with a structured failure message + agent-ready remediation Markdown
- Corpus entries grow to a reasonable starter set (~50 attack strings across categories)

**DoD:**
- All 15 deterministic tests pass against a known-safe mock chatbot
- Each test fails predictably against a known-vulnerable mock chatbot (a fixture that intentionally fails specific patterns)
- 80%+ coverage on the plugin code

### Session 3 — Optional LLM-judge tests (DeepEval extra)

**Scope:** Add the 5 optional tests that require an LLM judge.

**Deliverables:**
- `src/pytest_wardenbot/grading/judge.py` — DeepEval wrapper
- `src/pytest_wardenbot/tests/test_semantic.py` — 5 semantic checks (semantic equivalence, brand alignment, hallucination, off-policy answer, refusal quality)
- DAG-style rubrics following the three-layer-grading discipline from the main plan (`15-continuous-monitoring.md §4.1`)
- Clear documentation about API key requirement
- Tests are auto-skipped if `[judge]` extra isn't installed

**DoD:**
- `pip install pytest-wardenbot[judge]` works
- LLM-judge tests run against a known-correct chatbot using a user-supplied API key
- LLM-judge tests skip gracefully if extra not installed or API key missing
- Cost-per-run for the 5 LLM-judge tests is documented (~$0.02 against Haiku)

### Session 4 — DX polish + quickstart command

**Scope:** Make the install-to-first-test path under 60 seconds.

**Deliverables:**
- `pytest-wardenbot --quickstart` CLI command (via pytest entry point) that generates a starter `conftest.py` + `test_my_bot.py` in the current directory
- Industry templates: e-commerce, SaaS support, generic
- Verbose failure messages with agent-ready Markdown
- Custom pytest markers: `@pytest.mark.wardenbot`, `@pytest.mark.severity('high'|'medium'|'low')`
- `examples/` directory with 3 worked examples (basic HTTP, OpenAI Assistant, GitHub Actions integration)
- Updated README with the install-to-first-test quickstart

**DoD:**
- A new user can go from `pip install pytest-wardenbot` to first green test in under 60 seconds (verified by stopwatch)
- Examples all run cleanly against test fixtures

### Session 5 — CI + repo polish

**Scope:** GitHub repo hygiene that makes the project look maintained.

**Deliverables:**
- GitHub Actions: lint (`ruff`), typecheck (`pyright` strict), tests across Python 3.11/3.12/3.13, coverage report
- `pre-commit` config
- Issue templates (bug report, feature request, test request)
- Pull request template
- `CONTRIBUTING.md` with the "out of scope" rule documented (anything the SaaS does is out of scope for the plugin)
- `CODE_OF_CONDUCT.md`
- `SECURITY.md` (per pytest-dev convention)
- Badges in README: PyPI version, Python versions supported, CI status, coverage, downloads/month

**DoD:**
- All CI runs green
- 85%+ test coverage
- Pre-commit hooks catch issues locally

### Session 6 — Docs site

**Scope:** A real documentation site, not just a README.

**Deliverables:**
- `mkdocs-material` setup in `/docs`
- Site published via GitHub Pages at `pytest-wardenbot.wardenbot.ai` (or `wardenbot.github.io/pytest-wardenbot` for v0.1)
- Pages: Quickstart (60-second install-to-test), Per-test docs (what each test catches, why it matters), Adapter how-to ("add your chatbot"), Custom tests how-to, FAQ, Changelog, API reference auto-generated
- Cross-link from README

**DoD:**
- Docs site live and navigable
- Quickstart actually works for a new user following only the docs

### Session 7 — Launch

**Scope:** Coordinated PyPI release + community launch sequence.

**Deliverables:**
- PyPI release of `pytest-wardenbot 0.1.0`
- GitHub repo: tagged release with release notes
- Submit PR to pytest-dev/pytest plugin list
- Launch posts (drafted in advance; published staggered):
  - Day 1: personal dev.to / blog post
  - Day 2: /r/Python "Show /r/Python"
  - Day 3: /r/LangChain "I built a pytest plugin for chatbot testing"
  - Day 4: Show HN
  - Day 5: Twitter/X with demo GIF
- Day 6–7: DM ~20 LLM-app maintainers with personalized pitch
- README updated with marketing-flavored hero section

**DoD:**
- Package live on PyPI; `pip install pytest-wardenbot` works for anyone
- First external star / issue / install
- Listed on pytest's official plugin list

---

## Working metrics (month 1 post-launch)

From the plugin-distribution research:

| Metric | Healthy | Failing |
|---|---|---|
| PyPI installs/week | 500+ | <100 |
| GitHub stars | 100+ | <20 |
| Inbound real-user issues | 5+ | 0 |
| External blog mentions | 3+ | 0 |
| Click-through on upgrade footer | 2%+ | <0.5% |

If at week 6 we're <100 installs/week with no external mentions, the *framing* is wrong (not the channel). Re-evaluate before sinking more time.

---

## Out of scope for v0.1 (deferred to v0.2+)

To keep v0.1 launchable, these wait:

- RAMPART runner (v0.2 — gated to `[agentic]` extra)
- LangChain / MCP / Slack / Discord adapters (v0.2)
- Authenticated "run against your WardenBot AI subscription" mode (v0.3, requires the SaaS to exist)
- Custom corpus uploads (v0.3 — Sentry+ tier feature)
- Multi-turn attack chains beyond basic (v0.2 — full PyRIT integration)
- Browser-recon adapter (Castle/Sentry feature; doesn't belong in OSS)
- `pytest-wardenbot --report-html` (nice-to-have, defer to v0.2)
- pytest-html report integration (v0.2)

---

## Upgrade CTA strategy (waitlist for now)

Until the managed SaaS launches, the plugin's footer message on every test run:

```
30 tests passed. ✅
Want continuous monitoring across all your bots, daily? Join the waitlist → wardenbot.ai/waitlist
```

No "subscribe now" CTAs. No bait-and-switch. The plugin's value stands alone; the upgrade path is honest about the SaaS not being live yet.

When the SaaS launches in Wave 6, this footer changes to a real signup link.

---

## Maintenance commitment

- ~10–20% of one engineer's time, indefinitely.
- Triage rule: anything the SaaS will do is **out of scope** for the plugin (documented in CONTRIBUTING.md). Politely decline PRs that try to add SaaS-like features.
- Monthly minor release on the second Tuesday: dependency updates, small test additions, bug fixes.
- Major releases (breaking changes): no more than annually, with 6-month deprecation warnings.
- Telemetry: **opt-in only**. Never default-on. Never phone home.
- License: Apache 2.0 forever. **No license rugpulls** (HashiCorp / ElasticSearch / MongoDB taught the lesson).

---

## TL;DR

7 sessions, ~2–3 weeks of focused founder time, to ship `pytest-wardenbot 0.1.0`. Defer RAMPART to v0.2 (alpha software + agentic-only relevance). Ship 15 deterministic tests + 5 optional LLM-judge tests. Lean install, frictionless first-test path, honest waitlist CTA. Start today with Session 1.
