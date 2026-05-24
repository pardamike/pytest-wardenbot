# Launch posts

Drafts for the v0.1 public launch. Each file is calibrated to its
platform's voice; Mike copy-pastes and edits before posting.

| File | Where | Format |
|---|---|---|
| [`hn-show.md`](./hn-show.md) | news.ycombinator.com → Show HN | Title + body |
| [`dev-to.md`](./dev-to.md) | dev.to | Long-form technical post |
| [`reddit-python.md`](./reddit-python.md) | /r/Python (Showcase flair) | Short-form Show |
| [`reddit-langchain.md`](./reddit-langchain.md) | /r/LangChain | Domain-specific post |
| [`twitter-thread.md`](./twitter-thread.md) | Twitter / X | 9-tweet thread |
| [`maintainer-dm.md`](./maintainer-dm.md) | DM to related-project maintainers | Personalized warm outreach |

## Posting cadence (suggestion)

1. **Day 0 (Monday).** Show HN goes live first thing. Be present in the
   comments for the first 6 hours — that's where the real signal is.
2. **Day 0 evening.** Tweet the thread. Tag the HN URL in the last
   tweet.
3. **Day 1 morning.** /r/Python and /r/LangChain posts. Don't cross-post
   simultaneously — Reddit's algorithm penalizes that.
4. **Day 1–3.** Maintainer DMs to 5-8 hand-picked people. Quality over
   quantity.
5. **Day 3.** dev.to long-form post lands. Cross-reference the HN
   discussion.

## What NOT to post

- Don't post on Twitter, HN, /r/Python all in the same hour. Reddit and
  HN don't reward synchronized launches.
- Don't post the LangChain post on /r/LocalLLaMA — wrong audience.
- Don't post the dev.to post until you've had 24+ hours of HN signal
  to incorporate into the framing.

## Before posting (each platform)

- Verify all links resolve (especially `wardenbot.ai/intake/` and
  `pypi.org/project/pytest-wardenbot/`).
- Re-read for the platform's voice — HN tolerates more honesty, Twitter
  demands hooks, Reddit dislikes anything that smells like
  "Show /r/X but it's a sales pitch."
- Make sure the latest test count + features match what's actually in
  the released version. If you've shipped 0.1.1 or 0.2 by the time of
  the post, update the numbers.
