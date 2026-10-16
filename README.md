# Does Hype Sell Games? The Metroid Case

Final project of the WBS Coding School Data Analytics Bootcamp · Javier Villanueva · October 2026

Metroid has a passionate fan base but has always sold modestly. This project measures the hype around the reveal of 55 Nintendo games, tests which parts of it go with sales, and estimates what that could mean for *Metroid Ravenous*.

**[Interactive dashboard (Data Studio)](https://datastudio.google.com/reporting/8513a2a0-515f-4f43-9db9-c7558b236bc5)** · **[PDF version](dashboard/metroid-hype-dashboard.pdf)** · **[Notebook](metroid-hype.ipynb)**

## The question

*Metroid Ravenous* (title revealed on 9 September 2026, launch January 2027) generated a lot of buzz online. *Metroid Dread*, the previous 2D Metroid, sold 3.07M copies in its first 15 months. How much of the hype around a Nintendo game's reveal actually goes with sales, and how could Ravenous do compared with Dread?

## Key findings

- **Hype has two parts:** how many people care (the fan base a franchise already has) and how excited they are (the jump in interest when the game is revealed).
- **Only the first part goes with sales.** Controlling for time on sale, fan base before the reveal (rank correlation 0.59), attention in the month after the reveal (0.59) and trailer views per day (0.56) relate to sales. The reveal jump (−0.18), days of elevated interest, and trailer like and comment rates do not.
- **Small fan bases jump the most but sell the least.** Median sales are 4.0M, 8.4M and 15.1M copies for small, medium and large fan bases. Metroid is in the small group.
- **Ravenous could sell about 3.5M copies in two years** (80% range 1.3M to 9.3M), close to Dread. To sell more, Metroid needs more fans, not a louder reveal.
- **The result holds in the robustness checks:** other samples and age controls, leaving out any single game, and resampling whole franchises.

## Dashboard

Built in Data Studio from the tables the notebook exports to BigQuery (section 8). The first ten pages are for the presentation; the last three are for analysts.

| Page | Title |
|---|---|
| 1 | Does Hype Sell Games? The Metroid Case |
| 2 | 55 Nintendo games, three data sources |
| 3 | Hype has two parts |
| 4 | Dread had the biggest jump, yet 33 of 40 games sold more |
| 5 | Small fan bases jump most, big fan bases sell most |
| 6 | How many people care goes with sales, how excited they are doesn't |
| 7 | Pick any game and compare it with Ravenous (interactive) |
| 8 | Ravenous could sell about 3.5M copies in two years |
| 9 | Hype tells you how excited fans are, not how many there are |
| 10 | Thanks for your attention! |
| 11 | For analysts: the evidence |
| 12 | For analysts: the numbers |
| 13 | For analysts: methods and limits |

The live report reads from BigQuery Sandbox, where tables expire after 60 days (around mid-December 2026). The PDF in `dashboard/` is the permanent copy.

## Data

| Source | What | How |
|---|---|---|
| Wikipedia | Sales of the best-selling Switch and Switch 2 games (mostly Nintendo's official figures) | Scraped with `pd.read_html` |
| Google Trends | Daily search interest from 90 days before to 30 days after each title reveal | `pytrends` |
| YouTube Data API v3 | Views, likes and comments of each title-reveal trailer | `requests` |
| Google Sheets | Sample sheet built by hand: reveal dates, trailers, search terms | Exported as CSV |

**Sample (55 games)**
- **Main group (39):** 38 Nintendo-published games with 4M+ copies, plus Metroid Dread (3.07M).
- **Comparison group (15):** 13 Switch games under 4M drawn at random by sales band (fixed seed), plus the only two Switch 2 games under 4M, so the study does not only look at hits.
- **Case:** Metroid Ravenous (no sales yet).
- **Rules:** published by Nintendo (co-published games only if Nintendo handled most of the distribution), at least 30 days between the title reveal and the release, no Switch 2 Edition duplicates.
- **Analysis sample (40):** the 54 games with sales, minus 14 whose Google Trends signals were too weak to measure.

## Method

**Hype signals**

| Signal | How it is measured |
|---|---|
| Fan base | Franchise searches per 100 searches for "Nintendo", in the same Google Trends query, 90 days before the reveal |
| Attention in the month after | The same ratio in the 30 days from the reveal on |
| Reveal jump | Franchise interest in the 30 days after the reveal ÷ its normal level in the 90 days before |
| Days of elevated interest | Days (of 30) above the 90th percentile of the pre-reveal days |
| Trailer views per day, like rate, comment rate | YouTube statistics of the reveal trailer (Nintendo of America and the official Pokémon channel only) |

Google Trends scales every query to its own peak (0 to 100), so separate queries cannot be compared. Putting each franchise in the same query as "Nintendo" works like a common ruler, so the ratio can be compared across games. Franchise terms (for example "metroid") are used instead of game names, because people search for the series more than for the exact title.

**Analysis**
- Partial Spearman rank correlation of each signal with copies sold, controlling for time on sale, with 95% bootstrap intervals (2,000 resamples).
- Games split into three equal groups by fan base size (small, medium, large).
- OLS regression `log(sales) ~ log(1 + fan base) + years on sale` (n = 40, R² = 0.58), used for the Ravenous estimate with an 80% prediction interval.
- Robustness checks: per-metric samples, sales per year, leave-one-out, and franchise clustering (cluster bootstrap and one row per franchise).

## Limitations

The main ones (the full list is in section 9 of the notebook):
- Small, designed sample (40 games): intervals are wide, and small effects can't be ruled out.
- Correlation, not causation: big franchises also get more marketing, shelf space and coverage.
- Hype is measured only in the month after the title reveal, not until launch.
- Google Trends struggles with new or generic names. 14 games were left out, some of them big sellers.
- Sales figures are reported at different dates. Prices, bundles and discounts were not modeled.
- The Ravenous estimate has a wide range and does not include the Switch 2 player base or marketing.

## Next steps

- Compare the estimate with Ravenous's real sales after launch.
- Add other publishers and platforms to test whether the result holds beyond Nintendo.
- Measure hype over the whole period from the reveal to the launch.
- Add signals closer to purchase intent, such as pre-orders or wishlists, and analyze trailer comments to learn what players want.

## Related research

- Schaer, Kourentzes & Fildes (2026). *Analogy-based life cycle forecasts with pre-release buzz.* European Journal of Operational Research, 333(1), 88–100. Pre-release search traffic, combined with earlier games of the same series, improved video game sales forecasts by up to 21%, with useful information up to 18 weeks before release.
- Ruohonen & Hyrynsalmi (2017). *Evaluating the use of internet search volumes for time series modeling of sales in the video game industry.* Electronic Markets. For nearly 100 games, searches and weekly sales mostly move together in the same week, and search data adds only limited forecasting power.
- Burmester, Becker, van Heerde & Clement (2015). *The impact of pre- and post-launch publicity and advertising on new product sales.* International Journal of Research in Marketing, 32(4), 408–417. Across 3,336 video games, publicity worked better than advertising before launch, and advertising worked better after.
- Houston, Kupfer, Hennig-Thurau & Spann (2018). *Pre-release consumer buzz.* Journal of the Academy of Marketing Science, 46, 338–360.

This project adds a simple split to that work: the fan base a franchise already has versus the jump at the reveal.

## Repository structure

```
metroid-hype/
├── README.md
├── metroid-hype.ipynb      # full pipeline: collection, metrics, analysis and dashboard tables
├── requirements.txt
├── dashboard/
│   └── metroid-hype-dashboard.pdf
└── *.csv                   # inputs, collected data and dashboard tables
```

The first cell of the notebook lists every CSV file and the section that creates it.

## How to run

1. Install the packages: `pip install -r requirements.txt`
2. Open `metroid-hype.ipynb` and run all cells (**Kernel > Restart Kernel and Run All Cells**). With the saved CSV files, the notebook rebuilds the whole analysis without new downloads.
3. To download fresh data, set `REFRESH_WIKI` or `REFRESH_YOUTUBE` to `True` in section 1, or delete the CSV you want to rebuild. YouTube needs a `.env` file with `YOUTUBE_API_KEY=...`. Google Trends may return error 429: wait a few minutes and run the cell again. Fresh downloads can give slightly different numbers, because Wikipedia is updated and Google Trends samples its data.

## Tools

Python (pandas, NumPy, statsmodels, matplotlib, requests, pytrends, python-dotenv), JupyterLab, YouTube Data API v3, Google Sheets, BigQuery and Data Studio.

## Author

Javier Villanueva · [LinkedIn](https://www.linkedin.com/in/jvillanuevap) · [GitHub](https://github.com/jvillanueva-p)

*Non-commercial educational project, not affiliated with Nintendo. Metroid images in the dashboard © Nintendo. Other logos belong to their respective owners.*
