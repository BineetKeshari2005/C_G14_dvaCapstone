# Attention to Influence: YouTube Analytics Data Pipeline & Dashboard

> A comprehensive data analytics project that traces how internet attention is created, distributed, sustained, and converted into influence.

## 📖 Project Description
In the modern digital economy, attention is currency. This project analyzes over **78,586 YouTube trending videos** across four major markets (US, UK, Canada, and India) to uncover the structural and behavioral levers that drive virality. By analyzing the entire lifecycle of a video—from initial exposure to deep engagement and influence—this project provides actionable insights for content creators, marketers, and digital strategists.

## ❓ Problem Statement
Content creators and digital marketers often struggle to understand the mechanics of virality, frequently optimizing for vanity metrics like raw views without a strategy to convert that reach into lasting influence. 
**Why it matters:** In a highly competitive digital landscape, understanding the statistical drivers of engagement (posting times, title structures, tag volumes) empowers creators to maximize their ROI, build sustainable audiences, and transition from mere visibility to true digital influence.

## ✨ Key Features
- **End-to-End ETL Pipeline:** Automated extraction, thorough cleaning, and transformation of raw YouTube trending data.
- **In-Depth EDA & Statistical Analysis:** Correlation testing, hypothesis testing (e.g., weekend effect), and distribution analysis of engagement metrics.
- **Feature Engineering:** Developed composite metrics such as `virality_score`, `engagement_rate`, and `retention`.
- **Interactive Visualization:** Four dynamic Tableau dashboards featuring 16 KPIs and 24 advanced charts to explore geographic spread, attention sustenance, and influence conversion.
- **Predictive Modeling:** Statistical modeling to identify the key predictors of a video's virality.

## 🛠️ Tech Stack
- **Languages:** Python
- **Libraries:** Pandas, NumPy, Matplotlib, Seaborn, Scikit-learn
- **Tools & Platforms:** Jupyter Notebook, Tableau Desktop / Public, Git
- **Data Engineering:** Custom ETL scripts (`etl_pipeline.py`)

## 🏗️ Project Architecture / Workflow
1. **Data Collection:** Sourced localized trending video data (CSV files) for US, UK, CA, and IN.
2. **Data Cleaning (`02_cleaning.ipynb`):** Handled missing values, standardized formats, and treated extreme outliers.
3. **Exploratory Data Analysis (`03_eda.ipynb` & `04_statistical_analysis.ipynb`):** Uncovered correlation between views and likes, tested temporal variables (time of day, day of week).
4. **Feature Engineering (`05_final_load_prep.ipynb`):** Created critical calculated fields (e.g., virality bins, engagement ratios) and exported `final_dataset_tableau.csv`.
5. **Visualization & Storytelling:** Built a comprehensive Tableau dashboard narrative tracing the user journey from click to comment.

## 📊 Dataset Information
- **Source:** YouTube Trending Video Data (Collected via Kaggle/Drive)
- **Size:** 78,586 records
- **Key Attributes:** `country`, `publish_time`, `views`, `likes`, `comments`, `title_length`, `tag_count`, `virality_score`
- **Markets Covered:** United States, United Kingdom, Canada, India

## ⚙️ Installation & Setup
Follow these steps to replicate the environment and run the pipeline locally:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/C_G14_dvaCapstone.git
   cd C_G14_dvaCapstone
   ```
2. **Create a virtual environment (optional but recommended):**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   ```
3. **Install required dependencies:**
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn jupyter
   ```
4. **Download Raw Data:**
   Ensure the raw CSV files for US, UK, CA, and IN are placed in the `data/raw/` directory.

## 🚀 Usage
To run the analysis:
1. Start the Jupyter Notebook server:
   ```bash
   jupyter notebook
   ```
2. Navigate to the `notebooks/` directory and execute the notebooks sequentially:
   - `01_extraction.ipynb`
   - `02_cleaning.ipynb`
   - `03_eda.ipynb`
   - `04_statistical_analysis.ipynb`
   - `05_final_load_prep.ipynb`
3. To view the visualizations, open `tableau/You Tube - What create Attention.twbx` in Tableau Desktop, or visit the [Tableau Public Link](https://public.tableau.com/app/profile/md.sajjan8380/viz/YouTube-WhatcreateAttention/AttendtionDashbaord?publish=yes).

## 💡 Results & Insights
- **Visibility vs. Engagement:** Most attention is shallow. While 2.4 Billion views were analyzed, only a fraction converted into deep engagement. Likes scale linearly with views ($r = 0.80$), but comments do not ($r = 0.67$).
- **The Weekend Penalty:** Videos posted on weekends significantly underperform compared to weekdays.
- **Timing is Everything:** 18:00 (6 PM local time) is the universal peak hour for publishing to maximize reach.
- **The 1% Rule of Influence:** Approximately 1% of videos (~786 videos) accumulate roughly 44% of total views, demonstrating a heavy Pareto distribution.
- **Optimal Packaging:** Titles between 40–55 characters and tag counts between 15–25 yield the highest engagement lifts.

## 📂 Folder Structure
```text
C_G14_dvaCapstone/
│
├── data/                  # Contains raw and processed datasets
│   ├── raw/               # Original CSV files (CA, GB, IN, US)
│   └── processed/         # Cleaned and engineered datasets ready for Tableau
├── notebooks/             # Jupyter notebooks for ETL and analysis
│   ├── 01_extraction.ipynb
│   ├── 02_cleaning.ipynb
│   ├── 03_eda.ipynb
│   ├── 04_statistical_analysis.ipynb
│   └── 05_final_load_prep.ipynb
├── scripts/               # Python source code for data pipelines
│   └── etl_pipeline.py
├── tableau/               # Tableau workbooks and assets
│   ├── You Tube - What create Attention.twbx
│   └── readme.md          # Detailed dashboard documentation
├── docs/                  # Additional project documentation
├── reports/               # Key insight PDF reports
└── README.md              # Project overview
```

## 🔮 Future Improvements
- **Expand Market Scope:** Integrate data from non-English speaking markets to test the universality of the observed Pareto distribution.
- **Content Categorization:** Implement NLP on video titles/descriptions to segment engagement analysis by content vertical (e.g., Gaming vs. Education).
- **Survival Analysis:** Transition from a static "time-to-trend" metric to a dynamic survival model using hazard rates to better predict video lifespan.

## 🤝 Contributing
Contributions are welcome! If you have ideas for new features or improvements:
1. Fork the project.
2. Create your feature branch (`git checkout -b feature/AmazingFeature`).
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

## 📄 License
Distributed under the MIT License. See `LICENSE` for more information.

## 📬 Author / Contact
- **Name:** Your Name
- **LinkedIn:** [Your LinkedIn Profile](https://linkedin.com/in/yourprofile)
- **GitHub:** [Your GitHub Profile](https://github.com/yourusername)
- **Email:** your.email@example.com
