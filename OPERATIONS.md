# Operations

AI Privacy Scorecard runs itself on GitHub: Pages for hosting, Actions for every recurring job. Nobody needs to do anything for it to keep working. This file lists each automated job and how it fails safely, what to watch, and the keys Jack can turn later.

- **Live:** https://theguysaccount.github.io/ai-data-collection-audit-agency/
- **Repo:** https://github.com/theguysaccount/ai-data-collection-audit-agency

## Automated jobs

| Job | Workflow | When (UTC) | What it does | How it fails safely |
|---|---|---|---|---|
| Deploy | `deploy.yml` | every push to `main` (not docs-only), after each policy check, by hand | Unit, data and build tests; builds `dist/`; renders share cards; runs every page in headless Chrome at 1280px and 390px (self-test, no script errors, no sideways scrolling); deploys to Pages; re-runs the browser check on the live site; pings IndexNow | Any failed test or browser check stops before the deploy, so the last good site stays live. IndexNow problems are logged, never fatal. |
| Policy check | `check.yml` | Mondays and Thursdays 07:23 | Reads every document on every scorecard (robots.txt respected, 2 s between requests to a host, identified reader); verifies every quote word for word; compares each document's sentences with the last read; appends changes to `data/changes.json`; runs the tests; commits; starts `deploy.yml`; lists changed documents and missing quotes in one `needs-review` issue | A page that can't be read keeps its last good record and date and is marked "not read in the latest check"; the run still passes. A page that shrinks to a fraction of its words is treated as unreadable, not as a change. Failing tests stop the commit. A change is recorded once per document version, so re-runs are harmless. |
| Self-test | `selftest.yml` | Wednesdays 15:47 | Tests; the live site in headless Chrome; every sitemap URL answers 200 with its own canonical URL; the feed and open data parse; the IndexNow key is published; data freshness. If the policy check last ran more than 9 days ago it starts one; more than 16 days is a failure | On failure it opens (or comments on) one `ops-alert` issue, and GitHub emails the repository owner. It never changes the site. |
| Keep-alive | `check.yml` and `selftest.yml` → `scripts/keepalive.sh` | with each of those runs, even when they fail | Re-enables every scheduled workflow through the API; pushes an empty commit if the repository has been quiet for 45 days | GitHub disables schedules after 60 days without activity; the policy check normally commits twice a week anyway. |

GitHub skips and delays scheduled runs on this account (hours late, sometimes not at all). That is why the policy check has two weekly slots, every job is safe to re-run, and the self-test starts a check when the data is stale. Times above are when a run is *requested*.

Schedule check at launch: on 22 Nov 2026 the weekly self-test and the policy check each ran on their own schedule and passed (first runs at 17:13 and 19:56 UTC). The temporary cron lines that made them fire early were then removed.

## How documents are read

- `pipeline/fetch.py` sends `AIDataScorecardBot/1.0 (+repo URL)`, reads robots.txt for each host and never fetches a disallowed URL.
- If a site answers automated readers with a bot challenge or a refusal (401/403/429/503), the reader uses the newest Internet Archive (Wayback Machine) capture of the same URL, if one exists and is less than 180 days old. The scorecard then shows "Internet Archive copy of <date>" and links the capture, and that capture date is the as-of date. It never disguises itself as a browser and never retries through other routes. A robots.txt "disallow" means no reading at all, archived or not. A page read only from the archive stays good while its copy is under 180 days old; a page that was read directly and then starts refusing keeps its last direct read for 14 days. After that the page says the latest check could not read it, and the quotes keep their dates.
- At launch (22 Nov 2026) every document answered directly except Grok's two (x.ai refuses scripts; read from Wayback captures of 12 and 17 Nov).

## What to watch

- **`needs-review` issues.** Opened by the policy check when a document's text changed or a quote is no longer found. The scorecard already shows a banner and marks the quote; someone (or the routine below) should re-read the document and update `data/apps/<app>.json`.
- **`ops-alert` issues.** The self-test failed. The run link explains which check.
- **`correction` and `right-of-reply` issues.** Public reports from visitors. Check each against the cited document before changing anything; add accepted corrections to `data/corrections.json` (`[{"date": "YYYY-MM-DD", "app": "<slug>", "text": "what changed"}]`) so they appear on the change log and feed.
- **"Not read in the latest check"** on a scorecard's documents table: a publisher started refusing the reader or moved the page. If a URL moved, update it in the app's JSON.

## Re-reviewing a scorecard (by hand or by the routine)

1. `python3 -m pipeline.grab --refresh --find "<word>" <document URL>` to see the current text.
2. Edit `data/apps/<app>.json`: answers, summaries ("<Company> says ..."), quotes copied exactly from the text, and set `reviewed` to today.
3. `python3 -m pipeline.check --only <app>` then `python3 -m pipeline.check --offline --review --only <app>` (fails if any quote isn't found).
4. `python3 -m unittest discover -s tests`, commit, push. The deploy runs on push.

Rules the tests enforce: every criterion answered with a rubric answer; every answer other than "not stated" cites a passage; every listed document is quoted; no loaded words (spy, illegal, deceptive, steal and so on) in our own prose. Rules they can't enforce: summaries say what the company *says*, never what it does; quote the shortest passage that carries the claim; prefer policy and help pages over marketing pages (they churn).

## Keys Jack can turn later

Nothing below is needed for the site to work.

### 1. Custom domain (the domain rollout session's job)

The public address is one setting: the repository variable `SITE_URL`. Canonical URLs, sitemap, feed, share cards, badges and IndexNow all follow it; internal links are relative.

1. DNS at the registrar. Subdomain: `CNAME <name> → theguysaccount.github.io`. Apex: `A @ → 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153`, `AAAA @ → 2606:50c0:8000::153, 2606:50c0:8001::153, 2606:50c0:8002::153, 2606:50c0:8003::153`, and `CNAME www → theguysaccount.github.io`.
2. Tell Pages: `gh api -X PUT repos/theguysaccount/ai-data-collection-audit-agency/pages -f cname=<domain>` (or Settings → Pages → Custom domain), wait for the certificate, then `gh api -X PUT repos/theguysaccount/ai-data-collection-audit-agency/pages -F https_enforced=true`.
3. `gh variable set SITE_URL -R theguysaccount/ai-data-collection-audit-agency --body "https://<domain>"` (no trailing slash).
4. `gh workflow run deploy.yml -R theguysaccount/ai-data-collection-audit-agency`. Old github.io URLs redirect to the domain automatically.

### 2. Money

GitHub Pages is for non-commercial use, so the site stays free and has no checkout. The slot is a "Support this project" link in the footer and on the About page, hidden until set:

- `gh variable set SUPPORT_URL --body "<url>"` (and optionally `SUPPORT_LABEL`), then run `deploy.yml`. A GitHub Sponsors page needs the account owner to enroll (bank and tax details).
- A paid product (monitoring API or change alerts for compliance teams, or audit reports for startups) would need separate hosting, Stripe, and someone accountable for audit claims. Not built.

### 3. Analytics (activation metric)

Off by default, so the site can say "No cookies, no tracking". Activation = a visitor follows a quote to the company's own document (`source-click`). Also counted when on: `copy/steps-text` (opt-out steps copied), `copy/embed-code`, `share`, `filter/<answer>`.

- Create a GoatCounter site (needs an account), then `gh variable set GOATCOUNTER_URL --body "https://<code>.goatcounter.com/count"` and run `deploy.yml`. The footer and About page switch their privacy wording automatically.

### 4. Review routine (keeps scorecards current and adds apps)

AI-written work can't run inside Actions (no API key). Schedule a Claude Code routine weekly on Tuesdays, on this repository, with this prompt:

> In theguysaccount/ai-data-collection-audit-agency: read the open `needs-review`, `correction` and `right-of-reply` issues. For each changed document, re-read it with `python3 -m pipeline.grab --refresh`, update `data/apps/<app>.json` following OPERATIONS.md ("Re-reviewing a scorecard"), and close the issue with a short note of what changed. For a correction that the cited document supports, fix the scorecard and add an entry to `data/corrections.json`; otherwise reply with the passage that supports the current answer. If there is time, add one app from the backlog in OPERATIONS.md (only if its documents can be read within the rules). Run every test and push only if all pass. Never post anywhere else.

Backlog (popular apps to add): Duck.ai (DuckDuckGo), Meta AI on meta.com pages that robots.txt allows, Character.AI if its policy becomes readable without a browser, Pi, Poe, Notion AI, Snapchat My AI, Replika.

### 5. Search Console

Google: verify the site in Search Console (needs Jack's Google account; easiest with a custom domain) and submit `/sitemap.xml`. Bing, Yandex and others get every deploy through IndexNow. The host-root robots.txt for `theguysaccount.github.io` is rebuilt daily by the account's user-site repository and lists this site's sitemap.
