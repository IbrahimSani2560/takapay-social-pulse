# TakaPay — Social Pulse

A single-page dashboard that turns 660 raw social media posts about TakaPay (a fictional mobile-wallet brand) into something a non-technical brand manager can read in under a minute.

**Live demo:** https://takapaysentimentalanalysis.netlify.app/

---

## What I built

One self-contained HTML page (`index.html`) — no backend, no build step. It loads the cleaned dataset (embedded as JSON) and renders:

- **Overall sentiment score** and split (negative/neutral/positive)
- **Topic breakdown** — what people are actually talking about, ranked by volume and colour-coded by how negative each topic skews
- **A daily sentiment trend** across June
- **A platform breakdown** (Facebook, Reddit, TikTok, YouTube, Instagram, X, News/Media)
- **A filterable post feed** — filter by topic, sentiment, or platform to read the underlying posts yourself. Click any donut/bar chart segment to jump straight to that filter, or use **Reset filters** / **Export CSV** to clear or download the current view.
- **A methodology footer** — transparent about what was cleaned and why

## The insight I added, and why

Beyond sentiment + topics, I added a **competitor-comparison callout**: whenever a post directly compares TakaPay to its rival, NgoodPay, the sentiment is negative **100% of the time** (81 of 81 posts), with an average score of 17/100 — far below the 48/100 average everywhere else.

I chose this over other options (e.g. a simple "top negative posts" list) because it's **actionable in a different way than the failed-transaction problem**. Failed transactions are a reliability/engineering fix. The competitor gap is about agent network coverage and cashback offers — a business/ops fix. A brand manager reading only the overall sentiment number would miss that these are two separate problems needing two separate owners. Surfacing it separately, with sample quotes, makes that visible immediately.

## What I noticed about the data

The dataset had no missing values or invalid numbers, but it had three "quiet" quality issues — the kind that don't show up in a `.isnull().sum()` check:

1. **24 posts** where the `sentiment` label disagreed with the `sentiment_score` (e.g. labelled "positive" with a score of 46, which is neutral range). I treated `sentiment_score` as the source of truth and re-derived the label from it (≥60 positive, ≤40 negative, else neutral), since the score is more granular and less likely to have been mislabeled by hand.
2. **20 posts** sharing identical text across different authors — likely copy-paste or bot activity. I kept them (they're still real volume/signal) but flagged them, and they're visible with a "duplicate" badge in the post feed.
3. **61 posts** marked `brand_mention = True` but actually about unrelated topics (traffic, food, exam stress) — labelled `off_topic`. I excluded these from the sentiment/topic charts so they don't dilute the real signal, but kept them in the raw data and reported the count transparently rather than silently dropping them.

## What I'd improve with another week

- Proper text de-duplication/near-duplicate detection (the current check only catches exact text matches, not paraphrased repeats)
- A real backend/database instead of embedding JSON in the HTML, so new data could stream in rather than requiring a rebuild
- Bangla-aware topic modelling instead of relying on the pre-labelled `topic` column, to sanity-check whether the labels themselves are trustworthy
- A proper time-series anomaly detector to auto-flag days like June 12 and 24, where sentiment dipped, instead of requiring a human to eyeball the trend line
- User testing with an actual brand manager to see which chart they reach for first, and cut whatever they ignore

## Where AI helped, and where I overrode it

- AI helped scaffold the HTML/CSS/Chart.js structure quickly and generate the design token system (colour palette, typography pairing).
- I overrode the default styling direction AI reaches for by default (cream background + terracotta accent, which is a very common AI-generated template look) in favor of a palette grounded in the actual subject — Bangladesh's flag colours and the Taka currency symbol (৳) as a recurring visual motif — so the dashboard doesn't look like a generic template.
- I caught and corrected an initial chart rendering bug where the topic bar chart's y-axis labels were being clipped (e.g. "failed transaction" rendering as "iled transaction") — fixed by widening the axis and increasing chart height rather than shrinking the font, which would have hurt readability.
- The decision to treat `sentiment_score` as ground truth over the `sentiment` label, and to exclude (not delete) off-topic posts, were my calls — AI can compute a mismatch count, but deciding which field to trust and what "exclude vs delete" means for a real product is a judgment call I made and can defend.

---

## How to run it

Just open `index.html` in any browser — no install, no server needed.

## How to deploy it

Easiest option — [Netlify Drop](https://app.netlify.com/drop): drag `index.html` onto the page and you get a live URL in seconds. (Vercel or GitHub Pages work the same way if preferred.)
