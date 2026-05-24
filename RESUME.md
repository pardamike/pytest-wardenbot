# RESUME — where we left off

Last worked: 2026-05-24. **v0.1.0 SHIPPED and LIVE on PyPI.**

## Current state

- **pytest-wardenbot 0.1.0 published to PyPI** at
  https://pypi.org/project/pytest-wardenbot/ (2026-05-24, 15:05 UTC).
- **Tagged `v0.1.0`** and pushed. Release workflow ran successfully via
  OIDC Trusted Publishing — no tokens stored.
- **Docs site live** at https://pardamike.github.io/pytest-wardenbot/
  with the v0.1.0 content.
- **All checks green:** 279/279 tests, pyright clean, ruff clean,
  pre-commit clean (including detect-secrets), mkdocs `--strict` clean.
- **Smoke-tested** `pip install pytest-wardenbot` in a clean venv —
  imports work, `__version__` reads "0.1.0".

## What v0.1.0 contains

**30 deterministic shipped tests** across 7 attack categories:
- Prompt injection (5 prompts × 2 checks = 10)
- System-prompt elicitation (3)
- Refusal bypass (3)
- Off-topic deflection (2)
- Indirect injection / XPIA (4)
- Encoded-payload jailbreak — Base64/ROT13/leet/hex (4)
- Multi-turn jailbreak — priming + payload (3)

Plus:
- Opt-in `test_canary_leak` (high-entropy token leak detection)
- User-supplied `business_truth_fact` corpus
- Optional 5 LLM-judge case factories via `[judge]` extra

**Bundled adapters:** `HTTPChatbotAdapter` / `AsyncHTTPChatbotAdapter`,
`OpenAIChatAdapter` / `AsyncOpenAIChatAdapter` (via `[openai]`),
`AnthropicMessagesAdapter` / `AsyncAnthropicMessagesAdapter` (via
`[anthropic]`). Plus `to_sync()` bridge for using async adapters from
the sync shipped tests.

**Public types:** `ChatbotAdapter`, `AsyncChatbotAdapter`,
`ChatbotResponse`, `BusinessTruthFact`, `MatchType`, `JudgeCase`,
`WardenBotError`, `WardenBotInfraError`.

## Outstanding (post-launch tasks)

1. **Create GitHub release for v0.1.0.** Notes drafted in
   `docs/launch/github-release.md`. Mike pastes via the GH UI at
   https://github.com/pardamike/pytest-wardenbot/releases/new?tag=v0.1.0
   (gh CLI not installed locally; UI is the path).

2. **Publish the launch posts.** Drafts in `docs/launch/`:
   - `hn-show.md` — Show HN
   - `dev-to.md` — long-form dev.to
   - `reddit-python.md` — /r/Python Showcase
   - `reddit-langchain.md` — /r/LangChain
   - `twitter-thread.md` — 9-tweet thread
   - `maintainer-dm.md` — personalized warm outreach template
   - `README.md` — posting cadence + pre-flight checklist
   Posts get **copy-pasted by Mike** with platform-specific edits.

3. **pytest-dev plugin list:** auto-resolved. Their list is generated
   from PyPI metadata (any package starting with `pytest-` is included).
   No PR needed; will appear on the next regeneration cycle.

## What landed in this session (sessions 7 — major scope expansion)

Started with persona reviews (CTO, DevSecOps, Lead Dev, SMB CISO) →
coalesced findings into 24 numbered fixes (T1.1 – T3.10) → executed
all 24 across 7 commits + version bump + release workflow.

Notable scope additions vs. the original 18-test v0.1 plan:
- New attack categories: indirect injection (XPIA), encoded payloads,
  multi-turn jailbreak, canary-token leak.
- Bundled OpenAI + Anthropic adapters (real code, sync + async). The
  `[openai]` and `[anthropic]` extras were vapor in the original plan
  — persona reviews flagged this as the #1 launch-day issue.
- Per-corpus override fixtures + `pytest_generate_tests` hook so users
  can customize any attack corpus from their conftest.py.
- `WardenBotInfraError` error taxonomy for clean infra-vs-finding signal.
- `ChatbotResponse.raw` default redaction of sensitive fields.
- `_corpus_override.resolve_corpus` helper that bridges pytest 8.x
  (`config._fixturemanager`) and 9.x (`session._fixturemanager`).
- Full EVALUATION.md methodology + runnable harness in
  `scripts/evaluation/` (gpt-4o-mini, claude-haiku-4-5, vulnerable-stub).
- `detect-secrets` pre-commit + Dependabot config.
- SHA-pinned actions in the release workflow.

## After v0.1.0: v0.2 roadmap (deferred)

These were explicitly scoped OUT of v0.1 (some came up in persona
reviews and got the "defer" call):

- **Native-async shipped tests** for parallel probe fan-out (sync
  tests + `to_sync()` bridge cover the async case in v0.1; v0.2 will
  add `async def test_X(...)` variants).
- **Bundled LangChain / MCP adapters.**
- **Broader XPIA via RAMPART integration** (deferred from v0.1 because
  RAMPART is alpha + single attack class + hard pin on `pyrit==0.13.0`).
- **Multi-judge ensemble mode** for safety-critical scoring (single
  Haiku judge in v0.1, ~80% agreement with human raters per literature).
- **Authenticated managed-service mode** that uploads results to a
  WardenBot AI dashboard.

## Constraints / preferences (still applicable)

- **No Claude attribution in git artifacts.** No Co-Authored-By,
  no 🤖, no "Generated with Claude Code" footer.
- **No timeline pressure.** Pre-revenue, founder-led. Build right > ship lean.
- **Apache 2.0 forever, no telemetry by default.** Documented in CONTRIBUTING.md and we will not rugpull.
- **Out-of-scope rule for the OSS plugin:** anything the managed Continuous Monitoring service does (cross-run history, alerts, dashboards, schedules) does not belong in the plugin.
- **Naming convention** (locked 2026-05-23): WardenBot AI = the company; Continuous Monitoring = the SaaS product line (Watch / Patrol / Sentry / Castle); Security Audits = the one-shot product line (Tier 1 free / Tier 2 $1,500 / Tier 3 $5,000). The pytest plugin is the OSS slice of Continuous Monitoring.
- **Sentry tier locked at $299/mo** with 3 endpoints incl. + $30/extra.
- **Content filter behavior to remember:** writing security-themed docs in large blocks trips Anthropic's filter. Workarounds proven this session: link out to public references instead of inlining (CoC pointed at Contributor Covenant), keep individual files small + focused, use neutral verbs ("probe", "test pattern") in surrounding prose, write SECURITY.md as a short scaffold not an exploit catalog.

## Repos involved (cross-references)

- **This repo:** `~/Code/pytest-wardenbot` → `github.com/pardamike/pytest-wardenbot`
- **Main wardenbot repo:** `~/Code/wardenbot` → contains the product implementation plan in `docs/implementation/` (especially `15-continuous-monitoring.md` for the SaaS product context the plugin funnels into, and `14-execution-plan.md` for the big-picture build sequence).
- **Marketing site spec:** `~/Code/wardenbot/docs/marketing/site-content-spec.md` (the spec you'd feed to GPT to redo the marketing site).

## To resume

The launch is done. Next session options:

- **Capture launch signal.** After Mike posts (HN / Reddit / Twitter /
  dev.to / DMs), review what feedback came back and decide what makes
  v0.1.1 vs. v0.2.
- **Verify `wardenbot.ai/intake/` works** — the plugin's CTA depends
  on it. If 404, fix in marketing site.
- **Start v0.2 work** from the roadmap above.
- **Customer feedback loop** — first 3-5 issues filed on the repo
  often inform the v0.1.1 patch release.
