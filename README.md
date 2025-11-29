# Ethiopian Mobile Banking CX Insights

An analysis pipeline for collecting and preprocessing Google Play reviews for three Ethiopian banks: **CBE**, **BOA**, and **Dashen**. This repository contains scraping code, a preprocessing pipeline, and an EDA notebook with visualizations to help measure user experience and service quality over time.

**Status:** Task 1 — Data Collection & Preprocessing

---

**Project Goals**

- Collect user reviews and metadata from Google Play for three target bank apps.
- Clean and normalize the data to a reliable CSV for analysis.
- Clean non-english like Amharic reviews
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

```// filepath: c:\Users\Jose\Desktop\KAIM8\WEEK2\Code\Ethiopian Mobile Banking CX Insights\README.md

# Ethiopian Mobile Banking CX Insights — Task 1

Short summary
- Collect user reviews from Google Play for three Ethiopian banks (CBE, BOA, Dashen).
- Preprocess the scraped reviews for analysis (dedupe, missing values, date normalization, basic cleaning).
- Save processed CSVs and provide visualization notebook.

Minimum deliverables
- Scraped data: minimum 400 reviews per bank (≈1,200 total).
- Preprocessing script: Scripts/preprocessing.py
- Analysis notebook: Scripts/preprocessing_EDA.ipynb
- GitHub repository with .gitignore and clear commits on branch `task-1`.

Repository structure (key files)
- Scripts/
  - scraper.py — collects reviews via google-play-scraper
  - preprocessing.py — data cleaning pipeline (load, missing handling, dedupe, language filter, normalize, save)
  - preprocessing_EDA.ipynb — EDA and visualizations
  - config.py — app IDs, bank names, data paths, scraping config
- data/
  - raw/ — raw scraped CSVs (DATA_PATHS['raw_reviews'])
  - processed/ — processed CSVs (DATA_PATHS['processed_reviews'])
- .env — optional local overrides for app IDs
- .gitignore
- requirements.txt (recommended)

How it works — high level
1. Scrape
   - scraper.py fetches reviews, ratings, dates, user names and other metadata for each target bank app.
   - Target: 400+ reviews per bank. Raw output saved to data/raw/reviews_raw.csv.

2. Preprocess
   - removal of rows with missing critical fields (review_text, rating, bank_name)
   - remove duplicate reviews (by review_id when available)
   - filter out non-English / Amharic reviews (basic heuristic)
   - normalize review_date to YYYY-MM-DD and extract year/month
   - clean text and compute text_length
   - validate ratings (keep 1–5)
   - final CSV saved to data/processed/reviews_processed.csv

3. Visualize / EDA
   - preprocessing_EDA.ipynb contains plots: rating distribution, reviews-per-bank, review length distribution, reviews over time, average rating over time (CBE, BOA, Dashen).

Quickstart (Windows)
1. Create a venv and install deps
   - python -m venv .venv
   - .venv\Scripts\activate
   - python -m pip install -r requirements.txt

2. (Optional) Add API/config overrides to .env
   - CBE_APP_ID, BOA_APP_ID, DASHEN_APP_ID — defaults exist in config.py

3. Run scraper
   - python Scripts\scraper.py
   - Output: data/raw/reviews_raw.csv

4. Run preprocessing
   - python Scripts\preprocessing.py
   - Output: data/processed/reviews_processed.csv
   - The script prints a preprocessing report.

5. Open notebook
   - jupyter notebook Scripts\preprocessing_EDA.ipynb
   - or run the notebook in VS Code

Data schema (processed CSV)
- review_id, review_text, rating, review_date, review_year, review_month,
  bank_code, bank_name, user_name, thumbs_up, text_length, source

Git workflow recommendations
- Work on branch `task-1` until Task 1 is complete.
- Commit frequently and with meaningful messages:
  - e.g., "task-1: add scraper that fetches reviews for three banks"
  - e.g., "task-1: add preprocessing pipeline — handle missing values, dedupe"
- Include .gitignore to avoid committing data/, .env, __pycache__, .venv
- Push regularly and open a pull request when ready for review.

Notes & next steps
- Language detection is currently heuristic; for higher accuracy consider `langdetect` or `fastText`.
- Consider adding unit tests for preprocessing functions.
- Add requirements.txt listing packages used (pandas, numpy, google-play-scraper, matplotlib, seaborn, tqdm, python-dotenv).
- Ensure sensitive tokens or credentials are not committed — keep them in .env.

License
- Add a license file if required (MIT recommended for open source).

Contact / Author
- Project maintained in this repo. Use GitHub issues / PRs for discussions.
```
