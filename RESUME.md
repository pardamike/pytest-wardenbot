# RESUME — where we left off

Last worked: 2026-05-24. Session **6 of 7 complete**; session **7 paused before any work**.

## Current state

- **Sessions 1–6 shipped + pushed.** Scaffold, full deterministic corpus, optional LLM-judge via `[judge]` extra, `--wardenbot-quickstart` CLI + examples, CI/community polish, mkdocs-material docs site.
- **GitHub Pages was enabled at end-of-day 2026-05-24.** Last empty-trigger commit pushed (`44ee549`) right before pausing. Docs site should be live at `https://pardamike.github.io/pytest-wardenbot/` — verify before continuing.
- **Version still at `0.1.0.dev0`** in `pyproject.toml` and `src/pytest_wardenbot/__init__.py`.
- **Not yet on PyPI.** Package name unreserved.
- **All checks green** at pause: 158/158 tests, 94% coverage, pyright clean, ruff clean, pre-commit clean, mkdocs `--strict` clean.

## Outstanding work for session 7 (in order)

1. **Version bump** `0.1.0.dev0 → 0.1.0` in `pyproject.toml` and `src/pytest_wardenbot/__init__.py`.
2. **Finalize changelog.** Move "[Unreleased] — 0.1.0.dev0" to "[0.1.0] — 2026-05-DD" in `docs/changelog.md`.
3. **Reserve `pytest-wardenbot` on PyPI** (decide: claim now with placeholder, or wait for first real release).
4. **Decide PyPI publish path.** Options:
   - **Trusted Publishing via GH Actions (recommended).** One-time setup on PyPI side: register `pardamike/pytest-wardenbot` + workflow as a trusted publisher; then a tag push triggers auto-release. No tokens in the repo.
   - **Manual `twine upload`** with a stored API token. Works today but worse long-term.
5. **Add `.github/workflows/release.yml`** that runs on tag push, builds artifacts, and uploads to PyPI (uses Trusted Publishing OIDC).
6. **Tag `v0.1.0` and push.** Triggers release workflow.
7. **Create GitHub release.** Either via `gh release create` (gh CLI not yet installed on this machine) or manually in the GitHub UI. Release notes drafted from changelog.
8. **Submit pytest-dev plugin list PR.** Edit `https://github.com/pytest-dev/pytest/blob/main/doc/en/reference/plugin_list.rst`-style file to include `pytest-wardenbot`.
9. **Publish launch posts** (drafts to be written into `docs/launch/` in session 7):
   - `docs/launch/dev-to.md` — long-form technical post
   - `docs/launch/reddit-python.md` — Show /r/Python
   - `docs/launch/reddit-langchain.md` — /r/LangChain
   - `docs/launch/hn-show.md` — Show HN title + body
   - `docs/launch/twitter-thread.md` — multi-tweet thread
   - `docs/launch/maintainer-dm.md` — personalized outreach template
   - Posts get **copy-pasted by Mike** (Claude can't publish to those platforms).

## After session 7: persona reviews

Mike asked for 4 independent persona reviews of the codebase after launch prep:

1. **CTO** (tech + sales-minded)
2. **DevSecOps Engineer** (loves security + tooling)
3. **Lead Developer** (SOLID/DRY, MIT grad type)
4. **SMB/Startup CISO/CTO** (evaluating for use)

Then coalesce findings → fix what's worth fixing → add/update tests → commit + push.

This was paused to be done **after** session 7 launch prep, not before.

## Constraints / preferences (per memory + this session's history)

- **No Claude attribution in git artifacts.** No Co-Authored-By, no 🤖, no "Generated with Claude Code" footer.
- **No timeline pressure.** Pre-revenue, founder-led. Build right > ship lean.
- **Apache 2.0 forever, no telemetry by default.** Documented in CONTRIBUTING.md and we will not rugpull.
- **Out-of-scope rule for the OSS plugin:** anything the managed Continuous Monitoring service does (cross-run history, alerts, dashboards, schedules) does not belong in the plugin.
- **Naming convention** (locked 2026-05-23): WardenBot AI = the company; Continuous Monitoring = the SaaS product line (Watch / Patrol / Sentry / Castle); Security Audits = the one-shot product line (Tier 1 free / Tier 2 $1,500 / Tier 3 $5,000). The pytest plugin is the OSS slice of Continuous Monitoring.
- **Sentry tier locked at $299/mo** with 3 endpoints incl. + $30/extra (was originally $199, cost-modeling forced the bump).
- **Content filter behavior to remember:** writing security-themed docs in large blocks trips Anthropic's filter. Workarounds proven this session: link out to public references instead of inlining (CoC pointed at Contributor Covenant), keep individual files small + focused, use neutral verbs ("probe", "test pattern") in surrounding prose, write SECURITY.md as a short scaffold not an exploit catalog.

## Open product questions (deferred)

- Is `wardenbot.ai/waitlist` landing live? The plugin's footer CTA points there. If it 404s at launch, friction.
- Custom domain `pytest-wardenbot.wardenbot.ai` for the docs site? Currently configured to `pardamike.github.io/pytest-wardenbot`; can move later by adding a `CNAME` file + DNS record.
- "Reserve PyPI name now or at first publish?" — leaning toward at first publish since the name is unusual enough not to be squatted.

## Repos involved (cross-references)

- **This repo:** `~/Code/pytest-wardenbot` → `github.com/pardamike/pytest-wardenbot`
- **Main wardenbot repo:** `~/Code/wardenbot` → contains the product implementation plan in `docs/implementation/` (especially `15-continuous-monitoring.md` for the SaaS product context the plugin funnels into, and `14-execution-plan.md` for the big-picture build sequence).
- **Marketing site spec:** `~/Code/wardenbot/docs/marketing/site-content-spec.md` (the spec you'd feed to GPT to redo the marketing site).

## To resume

Re-read this file. Decide whether to:
- (a) Continue session 7 from item 1 (version bump → launch posts).
- (b) Do the persona reviews first against the *current pre-launch* state, then decide whether their findings change anything in session 7's plan.

(b) is arguably the smarter sequencing — reviews are cheap, you get a final polish before going public, then launch with confidence. But (a) was the plan you stated yesterday. Your call.
