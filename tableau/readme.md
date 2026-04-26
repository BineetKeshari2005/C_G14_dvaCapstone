# Attention to Influence — YouTube analytics dashboard report

A four-dashboard Tableau project that traces how internet attention is created, distributed across geographies, sustained through engagement, and eventually converted into influence.

---

## Project at a glance

| | |
|---|---|
| **Research question** | How does attention on the internet become influence? |
| **Dataset** | `final_dataset_tableau.csv` — 78,586 YouTube trending videos |
| **Markets covered** | United States · United Kingdom · Canada · India |
| **Tool** | Tableau Desktop / Public |
| **Theme** | YouTube dark — red `#FF0000`, surface `#1C1C1C`, canvas `#0F0F0F` |
| **Deliverable** | 4 dashboards × 4 KPIs × 6 charts = 16 KPIs + 24 charts total |

---

## Table of contents

1. [Dataset and prep](#1-dataset-and-prep)
2. [Key insight foundation](#2-key-insight-foundation)
3. [Dashboard 1 — What creates attention](#3-dashboard-1--what-creates-attention)
4. [Dashboard 2 — Attention across countries](#4-dashboard-2--attention-across-countries)
5. [Dashboard 3 — What sustains attention](#5-dashboard-3--what-sustains-attention)
6. [Dashboard 4 — Attention into influence](#6-dashboard-4--attention-into-influence)
7. [Final consolidated narrative](#7-final-consolidated-narrative)
8. [Design system](#8-design-system)
9. [How to rebuild](#9-how-to-rebuild)
10. [Limitations and next steps](#10-limitations-and-next-steps)

---

## 1. Dataset and prep

### Source columns

| Column | Type | Meaning |
|---|---|---|
| `country` | string | Market — US, UK, CA, IN |
| `publish_time` | datetime | When the video was uploaded |
| `views` | int | Total views accumulated |
| `views_log` | float | Natural log of views (for binning) |
| `views_capped` | int | Views with extreme outliers capped |
| `likes` | int | Total likes |
| `comments` | int | Total comments |
| `engagement_rate` | float | (likes + comments) / views |
| `like_ratio` | float | likes / views |
| `comment_ratio` | float | comments / views |
| `virality_score` | float | Composite score (0–1) |
| `attention` | float | Reach proxy = views |
| `retention` | float | Engagement weighted by reach |
| `influence` | float | Influence proxy from views and engagement |
| `title_length` | int | Characters in title |
| `title_uppercase_ratio` | float | Share of uppercase chars |
| `title_has_exclamation` | binary | Title contains `!` |
| `tag_count` | int | Number of tags |
| `has_tags` | binary | Any tags or none |
| `publish_hour` | int (0–23) | Hour of upload |
| `publish_day` | string | Weekday name |
| `is_weekend` | binary | 1 if Sat or Sun |
| `time_to_trend` | int | Days from upload to trending |

### Calculated fields used across dashboards

```text
Day Name           = DATENAME('weekday', [publish_time])
Day Sort           = DATEPART('weekday', [publish_time])
Weekend Label      = IF [is_weekend]=1 THEN "Weekend" ELSE "Weekday" END
Engagement Bin     = bin(engagement_rate, 0.01)
Virality Bin       = bin(virality_score, 0.05)
TTT Bin            = bin(time_to_trend, 1)
Tag Bucket         = "0", "1–5", "6–15", "16–30", "31+"
Top 1% Flag        = IF virality_score >= PERCENTILE(virality_score, 0.99) THEN "Top 1%" ELSE "Rest" END
Virality Tier      = "Viral" / "Strong" / "Mid" / "Low" by percentiles
```

---

## 2. Key insight foundation

Before designing the dashboards, EDA produced seven anchor findings that drive every chart choice:

| # | Question | Answer | Evidence |
|---|---|---|---|
| 1 | How does attention start? | Visibility, not quality | views ↔ virality r = 0.73–0.86, views ↔ likes r = 0.80 |
| 2 | Do people engage with everything that gets attention? | No, most attention is shallow | virality ↔ engagement r ≈ 0.01 |
| 3 | What separates shallow from real attention? | Engagement ratios | engagement ↔ like_ratio r = 0.99, ↔ comment_ratio r = 0.52 |
| 4 | First signal that content is working? | Likes | likes ↔ views r = 0.80, ↔ comments r = 0.67 |
| 5 | What shows deeper connection? | Comments | comments ↔ comment_ratio r = 0.57 |
| 6 | What boosts or hurts attention? | Hour, day, title, tags | weekend t = −27.9, p ≈ 0 |
| 7 | Can we fully predict virality? | Only partially | Model R² = 0.56 |

The four dashboards each express one leg of this story.

---

## 3. Dashboard 1 — What creates attention
![alt text](image-1.png)
### Purpose
Identify the structural and behavioural levers that allow a video to accumulate raw views. This is the "spark" stage — exposure before evaluation.

### KPIs

#### KPI 1 · Total views analysed
- **Value seen on dashboard**: `2.40 B`
- **Why this KPI?** Grounds every later ratio in absolute scale. Without a denominator, percentages are meaningless.
- **What it reveals**: The panel is large enough to support distributional and statistical claims.
- **Field**: `SUM(views)`
- **Format**: `#,##0.00,,"B"`

#### KPI 2 · Avg views per video
- **Value**: `30.5 K` (median 5.2 K)
- **Why this KPI?** A single mean hides distribution shape. Showing mean alongside the implied median gap flags skew.
- **What it reveals**: Mean is ~6× median, confirming heavy right skew (winner-takes-all).
- **Field**: `AVG(views)` with implied median callout
- **Format**: `#,##0.0,"K"`

#### KPI 3 · Peak publish hour
- **Value**: `18:00`
- **Why this KPI?** Reduces a 24-bar hour chart to one decision-grade number.
- **What it reveals**: 6 PM local outperforms early-morning slots by multiples.
- **Field**: hour with maximum `AVG(views)`
- **Format**: `00":00"`

#### KPI 4 · Weekend effect (t-stat)
- **Value**: `−27.9`
- **Why this KPI?** Quantifies the size and direction of weekend penalty in one number.
- **What it reveals**: Weekend posts significantly underperform; the sign and magnitude make it actionable.
- **Field**: external t-test result (or in-Tableau `Weekend Lift %` proxy)

### Charts

#### Chart 1 · Views distribution
- **Type**: Histogram on log bins
- **Axes**: X = `views_log` (bin size 0.5), Y = count of videos
- **Colour**: Sequential red, darkest at the high-views bin
- **Why this chart?** Log binning is the only way to honestly show a winner-takes-all distribution; linear binning compresses 99% of the data into the first bar.
- **Insight produced**: Clear right skew. Most videos cluster at low views; a long thin tail produces virality. The shape rules out averages as a primary KPI for the rest of the deck.

#### Chart 2 · Weekend vs weekday
- **Type**: Donut
- **Encoding**: angle = share of videos, colour = weekday vs weekend
- **Why this chart?** Donuts are the cleanest way to show a 2-way part-to-whole split.
- **Insight**: 71% of posts happen on weekdays — and those weekday posts capture a disproportionate share of the total views, reinforcing KPI 4.

#### Chart 3 · Avg views by publish hour
- **Type**: Area line
- **Axes**: X = `publish_hour` 0–23 (continuous), Y = `AVG(views)`
- **Why this chart?** Area lines convey both the cyclic shape and the magnitude — better than bars for a continuous time-of-day axis.
- **Insight**: Two peaks emerge — late-morning local and an evening peak at 18:00. Pre-dawn hours (3–6) collapse to near zero.

#### Chart 4 · Views by day of week
- **Type**: Vertical bar
- **Axes**: X = day name (Mon → Sun), Y = `AVG(views)`
- **Why this chart?** Bars let the eye rank days quickly and see the weekend cliff.
- **Insight**: Friday is highest. Saturday and Sunday drop sharply, mirroring the weekend t-stat.

#### Chart 5 · Title length vs views
- **Type**: Density scatter with polynomial trend line
- **Axes**: X = `title_length`, Y = `views` (log)
- **Why this chart?** Tests for a non-linear sweet spot; density encoding is necessary because the dataset has 78k points.
- **Insight**: Titles 40–55 characters perform best. Too short reads vague; too long reads cluttered.

#### Chart 6 · Tag count buckets — avg views
- **Type**: Horizontal bar
- **Axes**: Y = `Tag Bucket`, X = `AVG(views)`
- **Why this chart?** Bucketing smooths noise so the marginal-return curve is legible.
- **Insight**: Views rise with tag count up to the 16–30 bucket, then flatten and slightly fall — over-tagging signals spam to the algorithm.

### Dashboard 1 takeaway
Attention is given before it is earned. Distribution is Pareto-like, not Gaussian. Timing matters meaningfully (6 PM beats 4 AM by multiples; weekends carry a measurable penalty), and packaging matters too (titles have a sweet spot, tags have diminishing returns). None of these levers can force virality — but they all move the baseline odds of being shown.

---

## 4. Dashboard 2 — Attention across countries
![alt text](image-2.png)
### Purpose
Compare how attention behaves across the four markets. Volume leadership and engagement leadership can diverge — a single global strategy will miss those local signatures.

### KPIs

#### KPI 1 · Countries analysed
- **Value**: `4`
- **Why?** Sets scope before comparison so the reader knows the comparison is bounded.
- **Reveals**: A balanced four-country panel (~19.6 K videos each).

#### KPI 2 · Top by total views
- **Value**: `India`
- **Why?** Answers "who has the most eyeballs" — the volume question — first.
- **Reveals**: India contributes ≈ 38% of total views.

#### KPI 3 · Top by engagement rate
- **Value**: `UK`
- **Why?** Separates volume from quality so the reader doesn't conflate them.
- **Reveals**: UK leads on average engagement rate (≈ 4.8%).

#### KPI 4 · Total videos in panel
- **Value**: `78.5 K`
- **Why?** Ground-truth denominator.
- **Reveals**: Roughly equal sample per country, so cross-country differences reflect behaviour, not coverage.

### Charts

#### Chart 1 · Attention hot zones (world choropleth)
- **Type**: World choropleth
- **Encoding**: country fill = `SUM(views)`, sequential red ramp
- **Why this chart?** Maps are the most intuitive geographic encoding — readers locate countries spatially without reading labels.
- **Insight**: India is filled darkest red; the US fills bright red; UK and Canada are mid-tone — the volume hierarchy is visible at a glance.

#### Chart 2 · Virality score by country
- **Type**: Vertical bar
- **Encoding**: bar height = `AVG(virality_score)` per country, colour ramp matches map
- **Why this chart?** Bars rank countries on the same primitive as the map's colour, so the two charts cross-reference cleanly.
- **Insight**: India 0.91, US 0.74, UK 0.58, Canada 0.47 — virality ordering follows volume ordering.

#### Chart 3 · Engagement rate by country (lollipop)
- **Type**: Horizontal lollipop (bar + dot)
- **Why this chart?** Lollipops emphasise endpoints — ideal for ranking a single metric across few categories.
- **Insight**: UK 4.8% > India 4.1% > US 3.2% > Canada 2.8%. Volume leadership and engagement leadership do not match.

#### Chart 4 · Share of total views (stacked donut)
- **Type**: Donut with country segments
- **Encoding**: angle = `SUM(views)`, colour by country
- **Why this chart?** Reinforces market share in one shape and lets the reader verify the KPIs visually.
- **Insight**: India 38%, US 29%, UK 18%, CA 15% — two-country dominance.

#### Chart 5 · Publish hour × country heatmap
- **Type**: Heatmap (12-cell hour × 4-row country grid)
- **Encoding**: cell colour = `AVG(views)`
- **Why this chart?** Hour × country is intrinsically a matrix; heatmap is the canonical encoding.
- **Insight**: 18:00 is the universal evening peak, but the cycle shifts east-west with time zone. India peaks earliest in UTC; US shifts latest.

#### Chart 6 · Weekend share by country
- **Type**: 100% stacked bar
- **Encoding**: bar = country, segments = weekday vs weekend share of posts
- **Why this chart?** Normalises so countries compare on behaviour, not sample size.
- **Insight**: India creators post most on weekdays; UK and Canada post a higher proportion on weekends.

### Dashboard 2 takeaway
Geography reshapes both the scale and the texture of attention. Volume leaders are not engagement leaders. Hour patterns shift with time zone but the evening peak is universal. Weekend behaviour varies enough that a global "avoid weekends" rule is too blunt — country context matters.

---

## 5. Dashboard 3 — What sustains attention
![alt text](image-3.png)
### Purpose
Once a video is seen, does it provoke a response? This dashboard switches the unit of analysis from impressions to reactions, because most attention is shallow and only a fraction converts into likes — and a smaller fraction into comments.

### KPIs

#### KPI 1 · Avg engagement rate
- **Value**: `3.7%`
- **Why?** Headline conversion — what share of exposure becomes any reaction.
- **Reveals**: Most viewers don't react. The distribution is right-skewed but tighter than views.

#### KPI 2 · Avg like ratio
- **Value**: `3.4%`
- **Why?** Isolates the low-effort, fast-twitch response.
- **Reveals**: Almost identical to engagement rate, meaning likes dominate the engagement number — comments add only ~0.3pp.

#### KPI 3 · Avg comment ratio
- **Value**: `0.32%`
- **Why?** Isolates the high-effort signal of intent.
- **Reveals**: An order of magnitude rarer than likes — and therefore a stronger signal per unit.

#### KPI 4 · Retention index (median)
- **Value**: `0.041`
- **Why?** Robust central tendency for a skewed distribution. Mean would be inflated by viral outliers.
- **Reveals**: For half the panel, only ~4 reactions occur per 100 views.

### Charts

#### Chart 1 · Engagement rate distribution
- **Type**: Histogram with median reference line
- **Encoding**: X = `Engagement Bin` 0–20%, Y = video count, vertical white dashed line at median
- **Why this chart?** Histogram + median calls out skew without misleading with a mean.
- **Insight**: Distribution concentrates below 5%; long right tail mirrors the views histogram. Same Pareto shape, different metric.

#### Chart 2 · Views vs likes
- **Type**: Density scatter with linear trend (log–log)
- **Axes**: X = log(views), Y = log(likes)
- **Why this chart?** Log–log is the only honest way to verify a strong linear relationship across orders of magnitude.
- **Insight**: r = 0.80. More views reliably produce more likes — confirms the algorithmic loop where exposure begets reaction.

#### Chart 3 · Views vs comments
- **Type**: Density scatter with linear trend (log–log)
- **Axes**: X = log(views), Y = log(comments)
- **Why this chart?** Same construction as Chart 2 lets the reader compare the two slopes side by side.
- **Insight**: r = 0.67. Comments scale with views but with more noise and a shallower slope — depth doesn't keep up with reach.

#### Chart 4 · Engagement by publish hour (heatmap)
- **Type**: Hour × day-of-week heatmap
- **Encoding**: cell = `AVG(engagement_rate)`
- **Why this chart?** Reveals which slot drives reaction intensity, not just reach.
- **Insight**: Engagement peaks midweek at 18:00 — reach and engagement agree on evenings, but engagement is more concentrated than reach.

#### Chart 5 · Like ratio vs comment ratio (bubble)
- **Type**: Bubble chart, size = views
- **Axes**: X = like_ratio, Y = comment_ratio
- **Why this chart?** Two ratios in one view tests whether they move together or decouple.
- **Insight**: They move together at low values but decouple at the top — viral videos split into "clicky/entertaining" vs "discussed/divisive" archetypes.

#### Chart 6 · Tag count → engagement lift
- **Type**: Combo bar + line (dual axis)
- **Encoding**: bars = `AVG(views)` per tag bucket, line = `AVG(engagement_rate)`
- **Why this chart?** Two units in one view exposes whether tagging boosts reach and engagement together or separately.
- **Insight**: Both peak at 15–25 tags. Beyond that, views still rise slightly but engagement falls — over-tagging signals spam and dilutes the conversation.

### Dashboard 3 takeaway
Most attention is shallow. Engagement concentrates below 5%, and comments are an order of magnitude rarer than likes. The strong views–likes correlation confirms likes are mostly a scale signal; the weaker views–comments correlation tells us depth doesn't scale automatically. Over-tagging hurts engagement even as it lifts reach.

---

## 6. Dashboard 4 — Attention into influence
![alt text](image-4.png)
### Purpose
Close the loop. Influence is the rarest outcome, and the unit of interest shifts from average behaviour to the tail of the distribution.

### KPIs

#### KPI 1 · Avg virality score
- **Value**: `0.62`
- **Why?** Sets the baseline so the tail is visible by contrast.
- **Reveals**: The mean hides a heavy tail — see Chart 2.

#### KPI 2 · Top 1% videos
- **Value**: `786`
- **Why?** Counts the influential tail explicitly. Without a count, "top 1%" is abstract.
- **Reveals**: Those ~786 videos hold ~44% of total views — classic Pareto.

#### KPI 3 · Avg time to trend
- **Value**: `1.8 d`
- **Why?** Captures how fast winners emerge.
- **Reveals**: Attention either hits early or not at all.

#### KPI 4 · Model R² (predictability)
- **Value**: `0.56`
- **Why?** Defines the ceiling of what is forecastable. Without R², readers might over-trust the feature importance chart.
- **Reveals**: About half of virality is structural and learnable; the other half is emergent luck.

### Charts

#### Chart 1 · Attention → engagement → influence funnel
- **Type**: Funnel (centred bars, narrowing)
- **Stages**: views → engaged views → deeply engaged views → influence tier
- **Why this chart?** Funnels visualise drop-off between sequential stages — the canonical conversion shape.
- **Insight**: 2.40 B → 88.6 M → 8.1 M → 786. Each stage drops off by ~10–100×. The cliff is steep.

#### Chart 2 · Virality score distribution
- **Type**: Histogram with outlier callout
- **Encoding**: X = virality_score 0–1, Y = count, top 1% bar coloured amber to call out the tail
- **Why this chart?** Makes the viral tail visually obvious instead of hiding it in an average.
- **Insight**: Body is left-heavy; the amber callout sits far to the right. The mean (0.62) lies between, confirming why mean alone is misleading.

#### Chart 3 · Top drivers of virality
- **Type**: Horizontal coefficient bars (positive ramp + negative ramp)
- **Encoding**: bar length = coefficient, colour = sign
- **Why this chart?** Ranked coefficients read as a decision list for creators — they can act on each row.
- **Insight**: Likes (+0.80) and views (+0.73) dominate; weekend posting (−0.19) and uppercase titles (−0.12) hurt. Tag count is mildly positive (+0.28).

#### Chart 4 · Time to trend distribution
- **Type**: Histogram
- **Axes**: X = days from upload to trending, Y = count
- **Why this chart?** Shows the fast-or-fade shape directly.
- **Insight**: Most trending happens inside 2 days; after a week, odds collapse toward zero. Attention is impatient.

#### Chart 5 · Influence vs views (tier-coloured scatter)
- **Type**: Tier-coloured scatter with linear trend
- **Encoding**: X = log(views), Y = log(influence), colour = `Virality Tier`
- **Why this chart?** Tiering isolates the viral cluster so it doesn't get lost in the crowd.
- **Insight**: Viral tier clusters tightly top-right. Mid tier is noisy. Low tier fills the floor — separation is clean enough to act on.

#### Chart 6 · Top viral videos treemap
- **Type**: Treemap
- **Encoding**: tile area = virality_score, colour = virality_score
- **Why this chart?** Treemap lets a few individual outliers sit proportionally alongside the group — perfect for "the few that matter".
- **Insight**: A handful of Indian and US videos dominate the area. The influence set is small, geographically concentrated, and dramatically larger than the next tier.

### Dashboard 4 takeaway
Influence is a rare conversion. ~1% of videos cross into the viral tier; those ~786 clips carry almost half of the total views. The feature-importance bar gives creators a short decision list — optimise for like-generating hooks, avoid weekend drops, avoid clickbait uppercase titles. The R² of 0.56 is the honest caveat: better choices roughly double your odds, but no model closes the gap between strong content and true virality.

---

## 7. Final consolidated narrative

Read end to end, the four dashboards answer one question: how does an ordinary video become an influential one? The data splits the journey into four stages, each with its own drop-off rate and design implications.

| Stage | Dashboard | Drop-off | What the creator controls |
|---|---|---|---|
| 1. Visibility | D1 — what creates attention | n/a (entry) | Hour, day, title, tags |
| 2. Geographic reach | D2 — attention across countries | ~uniform per market | Local timing, weekend strategy |
| 3. Engagement depth | D3 — what sustains attention | views → reactions ≈ 4% | Hooks, format, tag balance |
| 4. Influence conversion | D4 — attention into influence | engagement → influence ≈ 1% | Speed (≤2 days) and structural levers |

> **Attention in the internet era is given before it is earned. Visibility starts the process, but only engagement sustains it, and only deep engagement ever converts into influence.**

### Stage-by-stage summary

**Stage 1 — Visibility.** Distribution is Pareto. Timing and packaging shift baseline odds. Most controllable stage.

**Stage 2 — Geographic reach.** Volume leadership ≠ engagement leadership. Time-zone shifts change the optimal hour. Country-aware strategy beats global rules.

**Stage 3 — Engagement depth.** Most attention is shallow. Likes scale; comments don't. At the top, "clicky" and "discussed" videos diverge into two playbooks.

**Stage 4 — Influence conversion.** Top 1% hold ~44% of views. Time-to-trend is short — winners emerge inside 1–2 days. Half of virality is predictable; the other half is luck.

---

## 8. Design system

### Colour tokens

| Token                  | Hex Code  | Where to Use                     |
| ---------------------- | --------- | -------------------------------- |
| **Canvas Background**  | `#0F0F0F` | Entire dashboard background      |
| **Card Background**    | `#1C1C1C` | KPI cards, chart containers      |
| **Border / Divider**   | `#2A2A2A` | Chart borders, section dividers  |
| **Primary Accent Red** | `#FF0000` | Main bars, primary highlights    |
| **Red Shade 1**        | `#FF3333` | Histogram bars                   |
| **Red Shade 2**        | `#E53935` | Secondary bars                   |
| **Red Shade 3**        | `#B71C1C` | Heatmaps                         |
| **Red Shade 4**        | `#7F1D1D` | Low emphasis bars                |
| **Dark Red**           | `#2E0B0B` | Background heatmap intensity     |
| **Amber Highlight**    | `#FFD54F` | Outliers / Top 1% viral callouts |
| **Positive Green**     | `#4CAF50` | Positive KPI delta               |
| **Negative Red**       | `#F44336` | Negative KPI delta               |
| **Primary Text**       | `#FFFFFF` | Titles, KPI values               |
| **Secondary Text**     | `#AAAAAA` | Axis labels, subtitles           |
| **Axis/Grid Lines**    | `#3A3A3A` | Gridlines, axis rulers           |

 
### Layout grid
- Fixed dashboard size: **900 × 990 px**.
- Header: 72px tall.
- KPI row: ~104px tall, 4 cards across.
- Chart rows: 3 rows of ~250–270px each, each row contains 1–2 charts.

### Interaction
- Country filter on every dashboard.
- Time-range filter on every dashboard.
- Cross-filter actions: clicking a country bar/tile filters the rest of the dashboard.
- Highlight actions where filtering would empty the view.

---

## 9. How to rebuild

1. Open Tableau → Connect → Text file → `final_dataset_tableau.csv`.
2. Set `country` geographic role to Country/Region; confirm `publish_time` is datetime.
3. Build the calculated fields in section 1.
4. For each dashboard:
   - Build the 4 KPI sheets first (single-number views).
   - Build the 6 chart sheets next.
   - Apply the colour tokens from section 8.
   - Assemble into a 900 × 990 dashboard with 1 header + 1 KPI row + 3 chart rows.
   - Add country and time-range filters; wire cross-filter actions.
5. Combine into a Story with one point per dashboard and the consolidated narrative as a closer.

Detailed step-by-step build instructions for each dashboard are documented in the project chat log; the structure above is the minimum recipe.

---

## 10. Limitations and next steps

- **Single platform.** Findings generalise to YouTube; replication on TikTok or Instagram would test whether the Pareto shape is platform-specific.
- **Four English-speaking markets.** Behaviour from non-English-speaking regions may differ — extending the panel is the highest-value next step.
- **Snapshot, not survival.** Time-to-trend is treated as a static field; a true survival model with hazard rates would lift the R² above 0.56.
- **No category dimension.** A topic/category column would let the engagement split be read by content vertical, which is the most-requested cut from creators.
- **No causal inference.** Coefficients are correlational; an A/B test on publish-hour or title-length would convert these from explanatory to prescriptive.

---

## Appendix · KPI / Chart inventory (24 charts + 16 KPIs)

| Dashboard | KPI | Chart 1 | Chart 2 | Chart 3 | Chart 4 | Chart 5 | Chart 6 |
|---|---|---|---|---|---|---|---|
| 1 — creates | Total views, Avg views, Peak hour, Weekend t-stat | Views histogram | Weekend donut | Hourly area line | Day-of-week bars | Title-length scatter | Tag-bucket bars |
| 2 — countries | Countries, Top by views, Top by engagement, Total videos | World choropleth | Virality bars | Engagement lollipop | Share donut | Hour×country heatmap | Weekend share stacked |
| 3 — sustains | Avg engagement, Avg like ratio, Avg comment ratio, Retention | Engagement histogram | Views vs likes scatter | Views vs comments scatter | Engagement heatmap | Like vs comment bubble | Tag count combo |
| 4 — influence | Avg virality, Top 1% count, Time to trend, Model R² | Conversion funnel | Virality histogram | Feature importance bars | Time-to-trend histogram | Influence vs views scatter | Viral treemap |

---
 
Tableau Public Link : https://public.tableau.com/app/profile/md.sajjan8380/viz/YouTube-WhatcreateAttention/AttendtionDashbaord?publish=yes