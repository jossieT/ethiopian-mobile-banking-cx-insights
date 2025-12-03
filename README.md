# Ethiopian Mobile Banking CX Insights

An analysis pipeline for collecting and preprocessing Google Play reviews for three Ethiopian banks: **CBE**, **BOA**, and **Dashen**. This repository contains scraping code, a preprocessing pipeline, and an EDA notebook with visualizations to help measure user experience and service quality over time.

**Status:** Task 1 — Data Collection & Preprocessing

---

**Project Goals**

- Collect user reviews and metadata from Google Play for three target bank apps.
- Clean and normalize the data to a reliable CSV for analysis.
- Clean duplicate data
- Clean non-english like Amharic reviews
- Remove emoji from the review text
- Provide exploratory visualizations showing rating trends, review volumes, and text statistics.

---

**Minimum Deliverables**

- Scraped raw data: at least **400 reviews per bank** (≈1,200 total).
- Preprocessing script: `Scripts/preprocessing.py` (handles missing values, dedupe, language filter, date normalization).
- Processed CSV: `Data/processed/reviews_processed.csv`.
- Analysis notebook: `Scripts/preprocessing_EDA.ipynb` (plots + EDA).

---

**Repository Structure (key files)**

- `Scripts/`
  - `scraper.py` — scraper using `google-play-scraper` to collect reviews and metadata
  - `preprocessing.py` — preprocessing pipeline (load → clean → dedupe → filter → normalize → save)
  - `preprocessing_EDA.ipynb` — notebook with EDA and plots
  - `config.py` — app IDs, bank name mapping, data paths, scraping configuration
- `Data/`
  - `raw/` — raw output from the scraper
  - `processed/` — cleaned datasets
- `.env` — optional local overrides (not committed)
- `.gitignore` — excludes `data/`, `.venv`, `.env`, and caches
- `requirements.txt` — required Python packages

---

**How it works — High Level**

1. Scrape

   - `scraper.py` fetches reviews, ratings, dates, reviewer name, thumbs-up counts and other metadata for each app.
   - Outputs combined raw CSV to `Data/raw/reviews_raw.csv`.

2. Preprocess

   - Handle missing critical fields (`review_text`, `rating`, `bank_name`).
   - Remove duplicates (prefer `review_id`, fallback to `review_text`).
   - Filter non-English / Amharic reviews (heuristic; optional replacement with a language detector).
   - Normalize `review_date` to `YYYY-MM-DD` and extract `review_year` and `review_month`.
   - Clean text and compute `text_length`.
   - Validate ratings (retain values 1–5).
   - Save final CSV to `Data/processed/reviews_processed.csv`.

3. Visualize / EDA
   - `preprocessing_EDA.ipynb` contains plots for rating distribution, reviews per bank, review-length distribution, reviews over time, and average rating over time for the three banks.

---

**Data Schema (processed CSV)**

- `review_id`, `review_text`, `rating`, `review_date`, `review_year`, `review_month`,
  `bank_code`, `bank_name`, `user_name`, `thumbs_up`, `text_length`, `source`

---

**Task 2 — Database & Analytics**

The project has been extended to include a PostgreSQL database integration and advanced insights generation.

1.  **Database Insertion**
    -   `Scripts/insert_into_tables.ipynb`: Handles the creation of tables (`banks`, `reviews`) and inserts the processed data from CSV files into the PostgreSQL database.

2.  **Results Display**
    -   `Scripts/display_results.ipynb`: Connects to the database and executes SQL queries to display key metrics such as total reviews per bank and average ratings.

3.  **Insights & Recommendations**
    -   `Scripts/insights_and_recommendaions.ipynb`: Performs advanced exploratory data analysis (EDA), sentiment analysis using TextBlob, and generates actionable recommendations based on the data. It includes visualizations for rating distributions and sentiment analysis.

---

**Quickstart (Windows)**

1. Create and activate a virtual environment:

```powershell
python -m venv .venv
.venv\Scripts\activate
```

2. Install dependencies:

```powershell
python -m pip install -r requirements.txt
```

3. (Optional) Add local overrides to `.env` (e.g. `CBE_APP_ID`, `BOA_APP_ID`, `DASHEN_APP_ID`).
4. Run the scraper:

```powershell
python Scripts\scraper.py
```

5. Run the preprocessing pipeline:

```powershell
python Scripts\preprocessing.py
```

6. Open the EDA notebook:

```powershell
jupyter notebook Scripts\preprocessing_EDA.ipynb
```

---

**Configuration**

- Defaults and paths are in `Scripts/config.py`. Use `.env` to override app IDs and scraping parameters.

**Notes & Next Steps**

- Language detection currently uses a heuristic to filter non-English (Amharic) content. For higher accuracy, consider integrating `langdetect` or `fastText`.
- Add unit tests for preprocessing steps (e.g., missing-handling, dedupe, date normalization).
- Consider persisting intermediate artifacts (parquet) for faster iteration.

---

**Sentiment Analysis (implementation)**

This project includes a lightweight sentiment analysis workflow implemented in `Scripts/sentiment_snalysis.ipynb`. The notebook demonstrates both lexicon-based and simple supervised approaches used during Task 1.

- Lexicon-based methods (implemented):
   - TextBlob: computes polarity and subjectivity for each review (`tb_polarity`, `tb_subjectivity`) and maps polarity to `tb_sentiment` labels (`positive`/`neutral`/`negative`). Useful for quick polarity scoring and prototyping.
   - VADER (NLTK's SentimentIntensityAnalyzer): computes a `vader_compound` score optimized for short social-style texts and reviews. Compound scores are mapped to `vader_sentiment` using standard thresholds (>= 0.05 → positive, <= -0.05 → negative, otherwise neutral).

- Supervised / ML-ready labeling (demonstrated):
   - A simple mapping from star ratings to sentiment labels is included for supervised experiments (`rating_to_label`): 1–2 → negative, 3 → neutral, 4–5 → positive.
   - The notebook shows how to prepare features (cleaned text, `CountVectorizer` and `TfidfVectorizer`), split data, and train baseline classifiers (e.g., `MultinomialNB`, `LinearSVC`).

- Outputs written in the notebook: additional columns appended to the processed DataFrame such as `tb_polarity`, `tb_subjectivity`, `tb_sentiment`, `vader_compound`, `vader_sentiment`, and `sentiment_label` (from rating). These columns can be exported to CSV for downstream analysis.

Usage notes and recommendations:
- The notebook uses `nltk` (VADER) and `textblob` for lexicon approaches; add these to `requirements.txt` if you plan to run the notebook.
- For production-quality sentiment classification consider:
   - Using a more robust language detector to pre-filter non-English text before sentiment scoring.
   - Fine-tuning or training transformer-based models (e.g., `distilbert`) if labeled data/supporting compute is available.
   - Evaluating model performance with a labeled validation set and standard metrics (precision/recall/F1) per class.

---

---

**Git Workflow & Best Practices**

- Work on branch `task-1` for Task 1 development; keep commits small and descriptive.
- Commit data changes only when necessary — prefer not to commit large raw data files.
- Use `.gitignore` to keep `Data/`, `.env`, and virtual environments out of version control.

---

**Acknowledgements & License**

- This project uses `google-play-scraper`, `pandas`, `numpy`, `matplotlib`, and `seaborn`.
- Add a `LICENSE` file if you plan to publish this repository. MIT is a permissive option.

---

If you want, I can also:

- Add badges (build, license) to the top of this README.
- Generate a `requirements.txt` from the environment.
- Create a short `CONTRIBUTING.md` with branching/PR guidelines.

---

**Contact**
Project maintained in this repository — open issues or PRs for questions and review.

