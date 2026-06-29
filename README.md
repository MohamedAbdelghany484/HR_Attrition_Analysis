# COVID-19 Data Exploration & Power BI Dashboard

## What this is

This was my first end-to-end data project — going from raw data, to SQL, to a Power BI dashboard. I used it to practice the core SQL skills (joins, CTEs, window functions, views) and to learn how to connect SQL Server to Power BI properly instead of just importing a CSV.

The dataset is the public COVID-19 data from Our World in Data (cases, deaths, vaccinations, by country and date).

## What I wanted to find out

I wasn't trying to publish new research — the goal was to ask the kind of questions an analyst would actually get asked, and answer them with SQL instead of a spreadsheet:

- Which countries got hit hardest relative to their population, not just in raw numbers?
- Was the death rate going up or down over time, country by country?
- How fast did vaccination coverage grow, and did that track with the drop in deaths?
- What do the headline global numbers look like if someone just wants the summary?

## How I built it

**Step 1 — Exploration in SQL.**
I started with simple `SELECT`s to understand the data, then moved into the more useful comparisons: total cases vs. deaths, infection rate vs. population, and so on. This part is just exploratory — it's in the script so you can see how I got from raw tables to the final logic.

**Step 2 — Views.**
Once I knew what calculations I needed, I turned the four most useful ones into SQL views so Power BI could just pull clean, ready-to-use tables instead of doing the math in DAX:

- `v_GlobalExecutiveSummary` — total cases, total deaths, global death rate (one row, for the KPI cards)
- `v_TopPandemicHotspots` — countries ranked by death-to-case ratio, using `DENSE_RANK()` so ties don't skip numbers
- `v_DailyInfectionGrowthRate` — day-over-day % change in new cases, using `LAG()` to compare each day to the one before it
- `v_PercentPopulationVaccinated` — running total of vaccinations per country over time, using a window function

**Step 3 — Power BI.**
Connected directly to the four views (Import mode) and built a 3-page report: a global overview, a hotspot/severity breakdown, and a trend page showing cases and vaccination progress over time.

## Things I'd flag myself

I'd rather point these out than have someone else find them:

- The `GlobalRecoveryRate` calculation is just `100 - DeathRate`. That's not a real recovery rate — a true one would need a `recovered_cases` column, which isn't in this dataset. I kept it as a rough/simplified number rather than removing it, but it shouldn't be read as medically accurate.
- I had an integer overflow issue early on — summing vaccination counts with `INT` breaks once the running total gets big enough (some countries' totals go into the billions). Switched to `BIGINT` in the views to fix it.
- Continent-level rows in the raw data show up as `NULL` in the `continent` column for some aggregate entries (not real countries) — I filter those out everywhere, but it's worth knowing the data has that quirk.

## Tools

SQL Server (T-SQL) for the exploration and views, Power BI Desktop for the dashboard.

## Files

```
FINAL_COVID_PORTOFOLIO_PROJECT.sql   — exploration queries + the 4 views
FINAL_COVID_PROJECT.pbix             — the dashboard
README.md                            — this file
```

## Running it yourself

1. Load the `CovidDeaths` and `CovidVaccinations` tables from [Our World in Data](https://ourworldindata.org/covid-deaths) into SQL Server.
2. Run `FINAL_COVID_PORTOFOLIO_PROJECT.sql` — it creates the 4 views at the end.
3. Open the `.pbix`, point it at your database, and refresh.

---

First real project combining SQL + Power BI — feedback welcome.
