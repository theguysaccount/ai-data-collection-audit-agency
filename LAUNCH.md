# Launch

Machine-driven mode: nothing below is posted, sent or bought by this project. Distribution runs through machine channels only.

## Live now

- https://theguysaccount.github.io/ai-data-collection-audit-agency/ (GitHub Pages, deployed by `deploy.yml`)
- Feed: `/feed.xml` · Open data: `/data/scorecards.json` · Badges: `/embed/<app>.svg`

## Production deploy steps (custom domain)

The domain rollout session owns this. The switch is one repository variable plus the Pages domain: see OPERATIONS.md, key 1, for the exact DNS records and commands. Nothing in the code changes.

## Machine distribution already running

- **Search pages people actually search for:** one page per app titled "Does <app> train on your chats?", with the opt-out question answered on the same page (no thin duplicates). FAQ, Article, Breadcrumb and Dataset structured data; canonical URLs; Open Graph cards rendered per app.
- **IndexNow** ping after every deploy (Bing, Yandex, Seznam, Naver). Host-root robots.txt for `theguysaccount.github.io` lists the sitemap daily (account user-site repo).
- **Answer engines:** every claim is a short, dated quote with a source link and a text-fragment link straight to the passage, which is the format AI answer engines cite.
- **Share loop:** share buttons, copyable opt-out steps that carry the page link, embeddable score badges per app.
- **Change feed:** RSS of every detected policy change, new scorecard and correction.
- **GitHub discovery:** repository description, topics and README.

## Search terms the pages target

"does chatgpt train on my data", "how to stop chatgpt using my chats", "gemini chat content setting", "does gemini train on my chats", "claude incognito chats retention", "grok private chat", "deepseek data stored in china", "perplexity ai data retention toggle", "le chat training opt out", "copilot ads chat history", "which ai apps train on your data".

## First 1,000 visitors (machine-only plan)

1. Week 1: IndexNow + host-root sitemap get the 12 pages crawled; the change log and feed give crawlers a reason to come back twice a week.
2. Weeks 2-4: each policy change produces a dated entry and an updated page, which is what news-driven searches ("Gemini privacy change") land on.
3. Ongoing: the review routine (OPERATIONS.md, key 4) adds one app a week from the backlog, each a new search entry point.

No social posts, outreach or ads. If Jack later wants human distribution, the obvious partners are privacy newsletters and procurement or compliance communities, who need dated, sourced answers; drafting for them is out of scope for this machine-run build.
