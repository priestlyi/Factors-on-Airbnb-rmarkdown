# Factors on Airbnb Pricing (Montreal Listings)

This project analyzes what drives Airbnb nightly prices in **Montreal** using a mix of:
- structured listing features (e.g., room type, capacity, bedrooms)
- text features from listing descriptions (TF–IDF, wordclouds, PCA)
- statistical modeling (AIC model selection + interaction testing)

The full analysis is written in **R Markdown** and rendered for GitHub viewing.

---

## 📄 View the Report

👉 **Rendered report:** [Factors on Airbnb Pricing](Factors%20on%20Airbnb%20Pricing.md)

---

## 🔎 What This Project Does

### 1) Data Loading
- Imports `listings.csv` (Inside Airbnb-style dataset for Montreal).

### 2) Data Cleaning + Feature Prep
- Converts variables into correct types (price, rates, numeric fields, factors).
- Flags data quality issues with **removal reason columns** for transparency.
- Filters to a cleaned dataset used throughout the analysis.
- Creates `log_price = log1p(price)`.

### 3) Exploratory Data Analysis (EDA)
- Summary stats (min/median/mean/max).
- Price distribution visualization.
- Demonstrates why `log(price)` is preferred (high right-skew in raw prices).

### 4) Text Analysis of Descriptions
- Splits listings into **High price vs Low price** using the median price.
- Tokenizes descriptions, removes stopwords, keeps sensible tokens.
- Produces wordclouds:
  - High vs Low price language
  - Overall listing language
- Builds an **Airbnb-specific stopword list** by identifying highly shared “generic” terms across groups.

### 5) TF–IDF + Group Comparison
- Constructs TF–IDF weights per listing.
- Compares average TF–IDF between High vs Low groups.
- Visualizes differentiating words via wordclouds of TF–IDF differences.

### 6) Dimension Reduction (PCA on TF–IDF)
- Builds a TF–IDF document-term matrix.
- Uses **`irlba::prcomp_irlba`** for efficient PCA on large text features.
- Interprets components using word loadings and loading-based wordclouds.
- Tests whether PC scores differ by price group (t-tests).

### 7) Statistical Modeling (Main Pricing Drivers)
- Fits candidate regression models on `log1p(price)`.
- Uses **AIC** to compare models.
- Tests a key interaction: **bedrooms × room_type** (ANOVA + AIC).
- Reports the final selected model with interpretation-ready coefficients.

---

## ⭐ Key Outputs
- Cleaned dataset with % retained
- Histograms of raw price vs log(price)
- Wordclouds: High vs Low language, overall language
- TF–IDF “difference” wordclouds
- PCA variance explained + PC2 interpretation via loadings
- Regression results + AIC/ANOVA model comparisons

---

## 📁 Repository Structure

- `Factors on Airbnb Pricing.Rmd` — Full analysis (code + narrative)
- `Factors on Airbnb Pricing.md` — Rendered report (GitHub-friendly)
- `Factors on Airbnb Pricing_files/` — Figures generated during knitting
- `listings.csv` — Input dataset (if included in the repo)

---

## 🧰 Libraries / Methods Used

**R packages**
- `tidyverse`, `dplyr`, `readr`, `stringr`, `tidyr`
- `ggplot2`, `scales`
- `tidytext`, `wordcloud`, `RColorBrewer`
- `irlba` (fast truncated PCA/SVD)
- `broom` (model tidying)

**Methods**
- rule-based cleaning with removal flags
- log transformation (`log1p(price)`)
- TF–IDF feature engineering
- PCA on TF–IDF (dimension reduction)
- linear regression
- model selection via AIC
- interaction testing via ANOVA

---

## ▶️ How to Run

1. Open the `.Rmd` file in RStudio
2. Ensure packages are installed (first run may take a bit)
3. Knit the report:
   - **Knit → Knit to GitHub Document** (recommended)
   - or Knit to HTML for local viewing

---

## 📌 Notes
- Prices are modeled using `log1p(price)` to reduce skew and lessen the influence of extreme outliers.
- Text results depend on the cleaning/token rules and the Airbnb-specific stopword list built in the analysis.

## ✅ Results Summary (Main Findings)

From the final regression model on **log1p(price)**:

- **Room type is a major driver of price.**
  - **Hotel rooms** are priced substantially higher than the baseline category (Entire home/apt).
  - **Private rooms** are priced lower than Entire home/apt.
  - (Shared rooms showed higher uncertainty in the final interaction model.)

- **Capacity matters a lot.**
  - Each additional guest capacity (**accommodates**) is associated with a higher nightly price, holding other variables constant.

- **Minimum nights has a negative effect.**
  - Listings requiring longer minimum stays tend to have **lower nightly prices**, suggesting hosts discount nightly rates when enforcing longer stays.

- **Bedrooms effect depends on room type (important interaction).**
  - Bedrooms increase price overall, but the **bedrooms premium is smaller for Private rooms** compared to Entire home/apt (significant interaction).
  - This supports the idea that “extra bedrooms” add less value in Private-room listings than in Entire homes.

- **Text features show systematic language differences.**
  - TF–IDF comparisons and PCA loadings indicate descriptions contain patterns linked to higher vs lower price groups, even though each individual component explains a small share of total variance (common in sparse text data).
