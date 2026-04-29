---

| Field | Details |
|---|---|
| **Project Title** | YouTube Analytics Data Pipeline & Dashboard |
| **Sector** | Social Media / Digital Marketing |
| **Team ID** | C_G14 |
| **Section** | C |
| **Faculty Mentor** | Satyaki Sir |
| **Institute** | Newton School of Technology |
| **Submission Date** | 28-04-2026 |

---

| Role | Primary Responsibility | Name | GitHub |
|---|---|---|---|
| **Project Lead** | Repo setup, timeline management, submission compliance, and ensuring the team hits Gate 1. | Krish Garg | |
| **Data Lead** | Dataset sourcing, raw data commit to GitHub, and owning the data dictionary in docs/. | Krish Garg | |
| **ETL Lead** | Notebooks 01 and 02 — Python extraction and cleaning pipeline. | Krish Garg | |
| **Analysis Lead** | Notebooks 03 and 04 — EDA and statistical analysis in Python | Srijan | |
| **Visualization Lead** | Tableau dashboard design, publishing to Tableau Public, sharing URL and screenshots. | Bineet Kesari & MD Sajjan | |
| **Strategy Lead** | Problem statement, KPI framework, and business recommendations. | Srijan Patel | |
| **PPT & Quality Lead** | Final report PDF, presentation deck, and contribution matrix. | Ronit Singh | |

---

## Business Problem
Content creators and digital marketers often struggle to understand the mechanics of virality, frequently optimizing for vanity metrics like raw views without a strategy to convert that reach into lasting influence. This project analyzes over 78,586 YouTube trending videos across four major markets (US, UK, Canada, and India) to uncover the structural and behavioral levers that drive virality. By analyzing the entire lifecycle of a video—from initial exposure to deep engagement and influence—this project provides actionable insights for content creators, marketers, and digital strategists.

**Core Business Question**

> What are the statistical drivers of engagement (posting times, title structures, tag volumes) that empower creators to maximize their ROI, build sustainable audiences, and transition from mere visibility to true digital influence?

**Decision Supported**

> Help content creators and digital strategists optimize publishing schedules, metadata (titles, tags), and engagement strategies to improve viewer retention and maximize their digital influence.

---

## Dataset
| Attribute | Details |
|---|---|
| **Source Name** | YouTube Trending Video Data |
| **Direct Access Link** | Kaggle/Drive |
| **Row Count** | 78,586 |
| **Column Count** | 8+ Key Attributes (`country`, `publish_time`, `views`, `likes`, `comments`, etc.) |
| **Markets Covered** | United States, United Kingdom, Canada, India |
| **Format** | CSV |

---

## KPI Framework
| KPI | Definition | Formula / Computation |
|---|---|---|
| Virality Score | Composite metric of overall video virality | Derived Feature (`virality_score`) |
| Engagement Rate | Level of active viewer interaction | (Likes + Comments) / Views |
| Retention | Sustained attention metric | Based on view longevity over time |
| Conversion to Influence | Ratio of superficial vs. deep engagement | Likes vs. Views / Comments vs. Views |

**Actual KPI Values:**

- **Total Views Analyzed:** 2.4 Billion
- **Views vs Likes Correlation:** $r = 0.80$
- **Views vs Comments Correlation:** $r = 0.67$
- **Total Records:** 78,586

---

## Tableau Dashboard
| Item | Details |
|---|---|
| **Dashboard URL** | [Tableau Public Link](https://public.tableau.com/app/profile/md.sajjan8380/viz/YouTube-WhatcreateAttention/AttendtionDashbaord?publish=yes) |
| **Executive View** | Features 16 KPIs and 24 advanced charts to explore geographic spread, attention sustenance, and influence conversion |
| **Operational View** | Analyzes engagement ratios, temporal publishing effects (time/day), and metadata optimization |
| **Main Filters** | Country (US, UK, CA, IN), Time of Day, Day of Week |

- **Top Publishing Time:** 18:00 (6 PM local time)
- **Top Metadata Setup:** 40–55 characters (Title), 15–25 tags
- **Engagement Skew:** Heavy Pareto distribution (1% of videos get 44% of views)
- **Temporal Trends:** Weekend penalty observed (weekdays perform better)

---

## Key Insights
1. **Visibility vs. Engagement:** Most attention is shallow. While 2.4 Billion views were analyzed, only a fraction converted into deep engagement. Likes scale linearly with views ($r = 0.80$), but comments do not ($r = 0.67$).
2. **The Weekend Penalty:** Videos posted on weekends significantly underperform compared to weekdays.
3. **Timing is Everything:** 18:00 (6 PM local time) is the universal peak hour for publishing to maximize reach.
4. **The 1% Rule of Influence:** Approximately 1% of videos (~786 videos) accumulate roughly 44% of total views, demonstrating a heavy Pareto distribution.
5. **Optimal Packaging:** Titles between 40–55 characters and tag counts between 15–25 yield the highest engagement lifts.

---

## Recommendations
| # | Insight | Recommendation | Expected Impact |
|---|---|---|---|
| 1 | The Weekend Penalty | Avoid publishing high-priority content on weekends; focus on weekdays. | Improved initial reach and algorithm pick-up. |
| 2 | Timing is Everything | Schedule video releases for 18:00 (6 PM local time). | Maximized immediate viewership and sustained momentum. |
| 3 | Optimal Packaging | Keep titles concise (40–55 chars) and tag counts moderate (15–25 tags). | Higher click-through rates (CTR) and better search ranking. |
| 4 | Deep Engagement | Actively prompt users to comment rather than just liking/subscribing. | Improved engagement metrics to combat shallow visibility. |

---

## Impact
- **Optimized Content Strategy:** Provides actionable metrics to transition from mere visibility to true digital influence.
- **Maximized ROI:** Helps creators target the exact structural levers (timing, tags, titles) that increase virality likelihood.
- **Audience Sustainability:** Shifts focus from vanity metrics (views) to sustainable audience building (engagement ratios).

---

## Repository Structure
```text
C_G14_dvaCapstone/
|
|-- README.md
|
|-- data/
|   |-- raw/
|   `-- processed/
|
|-- notebooks/
|   |-- 01_extraction.ipynb
|   |-- 02_cleaning.ipynb
|   |-- 03_eda.ipynb
|   |-- 04_statistical_analysis.ipynb
|   `-- 05_final_load_prep.ipynb
|
|-- scripts/
|   `-- etl_pipeline.py
|
|-- tableau/
|   |-- You Tube - What create Attention.twbx
|   `-- readme.md
|
|-- docs/
|-- reports/
```
