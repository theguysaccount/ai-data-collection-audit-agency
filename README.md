# AI Privacy Scorecard

**What AI chat apps say they do with your chats, quoted from their own policies and re-checked every week.**

Live: https://theguysaccount.github.io/ai-data-collection-audit-agency/

Each scorecard answers seven questions (training on your chats, how long chats are kept, deletion, people reading chats, sale and advertising, a no-history mode, and dated policies) on a [published rubric](data/rubric.json). Every answer cites the company's own passage, linked to the source. A weekly job re-reads every document, re-verifies every quote word for word, and logs changed text to a change page and an RSS feed. Facts and quotes only: no accusations, no testing of the apps, no legal advice.

Covered: ChatGPT, Claude, Gemini, Microsoft Copilot, Perplexity, DeepSeek, Mistral Vibe (formerly Le Chat), Grok. Apps whose documents can't be read within this project's rules are listed on the home page with the reason.

## Run, test, build

Python 3.9+ and no dependencies. Node 22+ and Google Chrome for the browser checks.

```bash
python3 -m unittest discover -s tests        # unit, data and build tests
python3 -m pipeline.check                    # read every document, verify every quote (network, ~6 min)
python3 -m pipeline.build                    # build the site into dist/
python3 -m pipeline.og dist                  # share cards (needs Chrome)
node scripts/e2e.mjs                         # every page in headless Chrome, desktop and phone
node scripts/e2e.mjs https://theguysaccount.github.io/ai-data-collection-audit-agency/
python3 scripts/check_live.py https://theguysaccount.github.io/ai-data-collection-audit-agency
```

Reviewer tools: `python3 -m pipeline.grab --find "train" URL` prints the lines of a document that match, read the same way the weekly check reads it; `python3 -m pipeline.check --offline --review` re-verifies every quote against cached copies.

## Deploy

GitHub Actions does everything (see [OPERATIONS.md](OPERATIONS.md)): `deploy.yml` on every push to `main`, `check.yml` twice a week, `selftest.yml` weekly. The public address is one setting, the repository variable `SITE_URL`.

## Layout

| Path | What |
|---|---|
| `data/rubric.json` | The rubric: criteria, answers, points, definitions |
| `data/apps/<app>.json` | One reviewed scorecard: documents, answers, quotes, opt-out steps |
| `data/state.json`, `data/sigs/`, `data/changes.json` | Written by the weekly check: how each document was read, each quote's status, sentence fingerprints, the change log |
| `data/not_covered.json` | Apps not covered, with the reason |
| `pipeline/` | `fetch.py` (polite reader), `check.py` (weekly check), `build.py` (site), `og.py` (share cards), `grab.py` (reviewer tool) |
| `scripts/` | Browser checks, live health check, IndexNow, keep-alive, review issues |
| `site/` | Static assets copied into the build |

Code MIT; scorecard text and data CC BY 4.0; quotes belong to their publishers.
