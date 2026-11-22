# Decisions

1. **A scorecard of published policies, not an "audit agency".** The idea was an independent auditor of AI data practices. A credible audit needs access, credentialed auditors and legal cover; none exists here, and calling the output an audit would overclaim. What machines can do honestly is read what companies publish, quote it exactly, score it on a fixed rubric and watch it change. The public name is "AI Privacy Scorecard"; nothing on the site says audit, certification or verdict.

2. **Facts and quotes, never accusations.** Every answer rests on a quote that must appear word for word in the cited document (checked by code). Summaries say "<Company> says ...". Tests reject loaded words in our own prose. Each page has a correction link and a right-of-reply link (public GitHub issue forms).

3. **Rubric fixed before scoring.** Seven criteria, 15 points, every answer defined in advance (`data/rubric.json`, shown at /rubric/). "Not stated" scores zero but is labelled as "not stated", never as a bad practice. The sale question separates "Sold" from "Sale not addressed" so silence is never shown as a sale.

4. **Polite reading only.** The reader identifies itself with the repo URL, follows robots.txt, waits between requests and never imitates a browser. When a site refuses scripts with a bot challenge, it uses the newest Internet Archive capture under 180 days old and labels it with the capture date; this is a public archive's own copy, not a way past the publisher's filter, and it is never used when robots.txt disallows reading. Apps whose documents can't be read this way (Meta AI, Poe, Pi, Character.AI) are listed as "not covered" with the reason and a link, instead of being scored from memory.

5. **Change detection by sentence fingerprints.** Each document's substantial sentences (30+ characters, minus volatile ones such as trace IDs and "updated N days ago") are hashed and stored in `data/sigs/`. Added and removed sentences mark a change; a few added sentences are kept as excerpts. Cheap, diffable in git, and robust to markup and whitespace churn. Marketing pages are avoided as sources because they churn.

6. **As-of date = the oldest date on which the quoted text was confirmed**, per scorecard. For archive reads, the capture date. Unreadable documents keep their last good date, so the as-of date ages visibly instead of silently.

7. **Static site, Python standard library.** No dependencies to rot. Internal links are relative, so one setting (`SITE_URL`) moves the whole site to a custom domain.

8. **Scope: consumer plans, U.S.-facing documents.** Business and API terms are noted as facts, not scored.

9. **Eight apps at launch** (the brief asked for five): ChatGPT, Claude, Gemini, Copilot, Perplexity, DeepSeek, Mistral Vibe, Grok. Claude is made by Anthropic, the maker of the model that built this site; it is scored by the same rubric and code as everyone else, and the data is open for anyone to check.

10. **Two weekly check slots plus a stale-data trigger** because GitHub drops and delays scheduled runs on this account.

## Open questions for Jack (none blocks the site)

- Nonprofit watchdog or paid certifier? This build is the watchdog shape; a certifier needs people.
- Any tie to ASI Readiness or ALIGNLAB? Not linked; the independence statement says no company it covers pays it. Linking a Persist venture would need a disclosure line.
- Domain: handled by the domain rollout session (OPERATIONS.md, key 1).
