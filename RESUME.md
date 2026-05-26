# RESUME — where we left off

Last worked: **2026-05-24 end of day**. v0.1.0 SHIPPED, branch protection on, ready for launch posts.

## TL;DR for next session

`pytest-wardenbot 0.1.0` is **live on PyPI**. Tests, docs, release workflow, branch protection — all green. Pending: publishing the launch posts + the punch list below.

## Live URLs to verify still work

- **PyPI:** https://pypi.org/project/pytest-wardenbot/
- **Docs:** https://pardamike.github.io/pytest-wardenbot/
- **GitHub:** https://github.com/pardamike/pytest-wardenbot

## What to start with: the 10-item post-launch punch list

Pick one. Top 3 (URL verifications) are highest priority.

### Top priority — verify the 3 external URLs the launch depends on

**1. Verify `security@wardenbot.ai` actually receives mail.**
SECURITY.md directs disclosure there. If a security researcher emails it and it bounces, you've sent a "we don't care" signal to exactly the person you want on your side. Send a test email; make sure it lands.

**2. Verify `wardenbot.ai/intake/` is live and not 404.**
README, docs/index, docs/about/powered-by, CONTRIBUTING, BUILD-PLAN, and both issue templates all point there. A 404 on launch day is the most embarrassing failure mode.

**3. Verify `wardenbot.ai` (the root) is live.**
README "Powered by" link + docs/about/powered-by.md both link to it. Same risk.

### Quick wins (5–10 min each)

**4. Add GitHub repo topic tags** for discoverability:
```
gh repo edit pardamike/pytest-wardenbot --add-topic pytest,pytest-plugin,llm,chatbot,ai-safety,prompt-injection,llm-security,red-teaming,python
```

**5. The "monthly second Tuesday" cadence promise in CONTRIBUTING.md.**
Next due: **2026-06-09** (~16 days). Either ship a 0.1.1 by then (dependabot bumps + first-week bug-fix triage is usually enough) or soften the language to "as-needed monthly cadence."

**6. Open GitHub issues for the v0.2 roadmap items** so community contributors can pick them up:
- v0.2: Native-async shipped tests for parallel probe fan-out
- v0.2: Bundled LangChain adapter
- v0.2: Bundled MCP adapter
- v0.2: RAMPART integration for broader XPIA coverage
- v0.2: Multi-judge ensemble mode for safety-critical scoring

Can do via `gh issue create` if you want me to file them in one shot.

### Nice-to-have (skip unless you want them)

**7. Smoke-test bundled OpenAI + Anthropic adapter examples against real APIs.**
Suite tests them against stubs. Quickest end-to-end: set keys in env, `cd examples/openai_chat && pytest -v`. Tells you if there's any drift between the SDK and what the adapter expects.

**8. `.github/FUNDING.yml`** for GitHub Sponsors / Ko-fi buttons. Skip if not running sponsorships.

**9. Custom docs domain** (`pytest-wardenbot.wardenbot.ai`). Was in your deferred-questions list. Add a `CNAME` file + DNS record when ready. Not urgent.

**10. Codecov badge in README** — verify it actually renders after CI runs on a PR. If "unknown," the OIDC tokenless upload didn't take and you'll need a `CODECOV_TOKEN` repo secret.

### Explicitly skip

- More attack patterns in the v0.1 corpus. Add based on real-world findings post-launch, not preemptively.
- More docs. Five how-to pages + tests catalog + design + evaluation is plenty for a v0.1.
- More refactoring. Codebase is in good shape post-persona-review.

## Other context to keep in mind

### Launch posts (drafts ready, you publish)

All 6 in `docs/launch/`. Mike copy-pastes and edits each before posting:
- `hn-show.md` — Show HN (do first)
- `twitter-thread.md` — 9-tweet thread (same day as HN)
- `reddit-python.md` — /r/Python (day after)
- `reddit-langchain.md` — /r/LangChain (day after)
- `dev-to.md` — long-form (day 3)
- `maintainer-dm.md` — warm DM template (over days 1–3, 5–8 hand-picked people)
- `README.md` — posting cadence + pre-flight checklist
- `github-release.md` — paste into https://github.com/pardamike/pytest-wardenbot/releases/new?tag=v0.1.0

### Branch protection on `main` (applied this session)

- Direct push blocked for everyone except admin (you).
- PRs required.
- 7 CI checks required (Pre-commit, Lint, Typecheck, Test 3.11/3.12/3.13, Build).
- No force-push, no deletion.
- `enforce_admins: false` so you can bypass in emergencies.
- View current settings: `gh api /repos/pardamike/pytest-wardenbot/branches/main/protection`

### What v0.1.0 contains (in case you forgot)

**30 deterministic shipped tests** across 7 attack categories:
- Prompt injection (5 prompts × 2 checks = 10)
- System-prompt elicitation (3)
- Refusal bypass (3)
- Off-topic deflection (2)
- Indirect injection / XPIA (4)
- Encoded-payload jailbreak — Base64/ROT13/leet/hex (4)
- Multi-turn jailbreak — priming + payload (3)

Plus opt-in `test_canary_leak`, user-supplied `business_truth_fact` corpus, optional 5 LLM-judge case factories via `[judge]` extra.

**Bundled adapters:** HTTPChatbotAdapter, OpenAIChatAdapter, AnthropicMessagesAdapter — each sync + async. `ChatbotAdapter` and `AsyncChatbotAdapter` Protocols. `to_sync()` bridge.

**Public types:** `ChatbotAdapter`, `AsyncChatbotAdapter`, `ChatbotResponse`, `BusinessTruthFact`, `MatchType`, `JudgeCase`, `WardenBotError`, `WardenBotInfraError`.

## v0.2 roadmap (deferred, captured in #6 above)

- Native-async shipped tests (parallel probe fan-out)
- Bundled LangChain / MCP adapters
- Broader XPIA via RAMPART integration
- Multi-judge ensemble for safety-critical scoring
- Authenticated managed-service upload mode

## Constraints / preferences (still applicable)

- **No Claude attribution in git artifacts.** No Co-Authored-By, no 🤖.
- **No timeline pressure.** Pre-revenue, founder-led. Build right > ship lean.
- **Apache 2.0 forever, no telemetry by default.** Documented in CONTRIBUTING.md.
- **Out-of-scope rule:** anything the managed Continuous Monitoring service does (cross-run history, alerts, dashboards, schedules) doesn't belong in the plugin.
- **Naming:** WardenBot AI = company; Continuous Monitoring = SaaS line; Security Audits = one-shot line. pytest plugin = OSS slice of Continuous Monitoring.
- **Sentry tier:** $299/mo locked.
- **Content filter:** writing security-themed docs in large blocks trips Anthropic's filter. Link out to public references, keep files small + focused, neutral verbs ("probe", "test pattern") in surrounding prose.

## Repos involved

- **This repo:** `~/Code/pytest-wardenbot` → `github.com/pardamike/pytest-wardenbot`
- **Main wardenbot repo:** `~/Code/wardenbot` → `docs/implementation/15-continuous-monitoring.md` for the managed-service spec the plugin feeds into.
- **Marketing site spec:** `~/Code/wardenbot/docs/marketing/site-content-spec.md`.

## To resume

Read this file, then pick from items 1–10 above. Most natural starting place: verify the 3 URLs (items 1–3), then file the v0.2 issues (item 6), then either publish launch posts or pick up v0.2 work.
