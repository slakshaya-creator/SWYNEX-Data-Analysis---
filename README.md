# SWYNEX-Data-Analysis---
This project analyzes about 215K patient drug reviews from the UCI Drugs.com dataset, cleaning the text and training sentiment models
# Drug Review Sentiment and Side-Effect Mining

Sentiment analysis and side-effect extraction on the UCI Drug Review dataset (Drugs.com), with an interactive Google Colab interface.

> Disclaimer: For research and education only. This is not medical advice and not pharmacovigilance.

# Overview

This project analyzes about 215K patient drug reviews. It cleans the text, trains sentiment classifiers, extracts reported side effects with a lexicon-based approach that handles negation, and connects the two to show which side effects are most associated with lower ratings. An interactive menu lets you query results after training.

# Features

- **Data loading:** downloads the UCI dataset automatically, or uses your own file (typed path or Colab upload). Falls back to a small synthetic demo dataset if the download fails.
- **Cleaning:** HTML entity decoding, URL removal, junk `condition` repair, duplicate removal, short-review filtering, and imputation of missing values.
- **Sentiment labels:** derived from rating (1-4 negative, 5-6 neutral, 7-10 positive).
- **Models:** Logistic Regression (TF-IDF only), Logistic Regression (TF-IDF + engineered features), and LinearSVC. Negations are kept (no stop-word removal). Optional zero-shot DistilBERT comparison on positive vs negative reviews.
- **Side-effect mining:** a 32-category lexicon (nausea, insomnia, weight gain, dry mouth, and others) with clause-aware negation detection, so "no nausea" is counted as negated, not reported.
- **Insights:** most reported side effects, mean rating shift when an effect is mentioned, drug-by-side-effect heatmap, and sentiment by condition.
- **Interactive menu:** drug report, analyze your own review, compare drugs.

# Quick start (Google Colab)

1. Open a new Colab notebook.
2. Paste the full contents of `drug_review_interactive.py` into one cell.
3. Run the cell and answer the setup prompts (press Enter to accept defaults).
4. Use the menu that appears after training.

No manual installs are needed on Colab. For local use, install:

```
pip install numpy pandas scipy scikit-learn matplotlib seaborn requests
pip install transformers torch   # only for the optional DistilBERT comparison
```

# Setup prompts

 Prompt | Default | Notes 
 Data source | 1 | 1 = download UCI, 2 = type a file path, 3 = upload (Colab)
 Max reviews | 50000 | Full dataset is about 215000; more rows means slower runs 
 Min reviews per drug | 30 | Drugs below this are skipped in drug-level tables 
 DistilBERT comparison | n | Downloads about 260 MB if enabled 
 Reviews for comparison | 300 | Only asked if the comparison is enabled 

## Interactive menu

| Option | Action |
1 .**Drug report:** review count, mean rating, sentiment split, top side effects with a chart 
2 . **Analyze your own review:** predicted sentiment with probabilities, reported and negated side effects 
3 . **Compare drugs:** side-by-side table for comma-separated drug names 
4 . Quit 

# Outputs

- `drug_reviews_clean.csv`: cleaned dataset with sentiment labels and extracted side effects
- `drug_summary.csv`: per-drug summary (reviews, mean rating, % positive, top side effects)
- Charts shown inline: missing values, rating distribution, class balance, confusion matrix, top sentiment words, side-effect frequency, rating impact, drug heatmap, sentiment by condition

# Pipeline

1. Load data
2. Raw EDA
3. Missing-value handling and text cleaning
4. Side-effect mining (lexicon NER with negation)
5. Labels, feature engineering, encoding
6. Clean-data EDA
7. Sentiment models and evaluation
8. Side-effect insights
9. Save outputs and launch the interactive menu

# Dataset

UCI Drug Review Dataset (Drugs.com), Gräßer et al., 2018. Columns: `drugName`, `condition`, `review`, `rating`, `date`, `usefulCount`. Source: https://archive.ics.uci.edu/dataset/462/drug+review+dataset+drugs+com

# Limitations
- Ratings are a noisy proxy for sentiment, and reviews are self-selected.
- The lexicon only finds side effects listed in `LEXICON`. Informal phrases (for example "zombie feeling") need to be added by hand.
- Negation uses a simple 3-word window, so complex sentences can be misjudged.
- A mentioned symptom is not proof the drug caused it. Dose, duration, and the underlying condition are not controlled for.
- Popular drugs dominate the data and can bias aggregate results.
- The DistilBERT model is general-purpose (trained on movie reviews), not medical.

# Possible extensions

- Fine-tune BioBERT or PubMedBERT for sentiment
- Train a token-classification NER model on annotated adverse-event data (CADEC, SMM4H)
- Normalize side effects to MedDRA terms
- Validate mined rates against SIDER or FDA labels
- Build a Streamlit or Gradio web interface
