# Maintainer DM — personalized outreach template

For warm DMs to maintainers of related projects (LangChain, DeepEval,
Promptfoo's successor at OpenAI, Garak, LiteLLM, etc.). Sent only when
there's a concrete reason — you've contributed to their project, they've
written about adjacent problems, or they've publicly said they wish a
pytest plugin existed.

Replace `{name}`, `{their-project}`, `{specific-thing}` before sending.

---

**Subject:** Pytest plugin for chatbot testing — would love your eyes

Hi {name},

Mike here (founder of WardenBot AI). I just shipped `pytest-wardenbot`,
an Apache-2.0 pytest plugin that runs 30 curated tests (jailbreak,
system-prompt leak, refusal-bypass, XPIA, encoded payloads, multi-turn,
canary leak) against any chatbot. I've been a {their-project} user
since {specific-thing} and your work on {specific-thing} is part of
why I think this is the right shape.

Why I'm writing: I'd love your eyes on it. Specifically:

- **{Project-specific question}** — e.g. "Does the way I'm modeling
  multi-turn session state in `ChatbotAdapter` map cleanly to how
  {their-project} thinks about conversation memory?" or "The async
  adapter Protocol — would you wire it differently?"

- **What's missing from the v0.1 corpus** that you've seen in your
  user base?

Not asking for a PR or any time investment. Even a 5-minute "yeah this
is fine" or "this is wrong because X" would be valuable signal.

The plugin is the OSS slice of a managed Continuous Monitoring service
I'm building (scheduled runs, dashboards, alerts — things a CI plugin
alone can't provide). I'm not pitching that — just transparent about
the business context. The plugin stands on its own and is the only
thing I'd ask you to look at.

- Code: https://github.com/pardamike/pytest-wardenbot
- Docs: https://pardamike.github.io/pytest-wardenbot/
- PyPI: https://pypi.org/project/pytest-wardenbot/

If it's interesting and you'd like me to attribute / link / cross-post,
happy to. If it's not your scene, no worries — thanks for shipping
{their-project} regardless.

Mike
mike@adelarintel.com
