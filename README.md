# A/B Testing — Landing Page Conversion Analysis

![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-statsmodels-8CAAE6?style=flat&logo=scipy&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat&logo=jupyter&logoColor=white)
![Kaggle](https://img.shields.io/badge/Dataset-Kaggle-20BEFF?style=flat&logo=kaggle&logoColor=white)

Statistical A/B test to determine whether a new landing page produces a meaningfully different conversion rate than the existing one — and whether the difference is worth acting on.

---

## The Problem

At first glance, two conversion rates can look different. The real question is: **is that difference real, or just noise?**

This project goes beyond surface-level comparison. It applies a rigorous statistical framework to avoid common traps like p-hacking, stopping early, or confusing statistical significance with business significance.

---

## Dataset

[A/B Testing Dataset — Kaggle](https://www.kaggle.com/datasets/zhangluyuan/ab-testing)

~294k user sessions with the following fields:

| Column | Description |
|--------|-------------|
| `user_id` | Unique user identifier |
| `timestamp` | Session timestamp |
| `group` | `control` or `treatment` |
| `landing_page` | `old_page` or `new_page` |
| `converted` | 1 if converted, 0 if not |

---

## Hypothesis — Defined Before Seeing Results

This is documented upfront to prevent p-hacking.

- **H₀:** The conversion rate of the new page equals that of the old page. Any observed difference is due to chance.
- **H₁:** The conversion rate of the new page is different from the old page.
- **Significance threshold:** α = 0.05

---

## Pipeline

```
Load data → Clean contaminated users → Remove duplicates →
Power analysis → Conversion rates → Z-test → Business conclusion
```

### 1. Data Cleaning

- Removed users assigned to `control` but shown `new_page` (1,928 rows)
- Removed users assigned to `treatment` but shown `old_page` (1,965 rows)
- Removed duplicate `user_id` entries (1 row)
- **Final dataset:** 290,584 rows

### 2. Power Analysis — Before Running the Test

Calculated required sample size before looking at results:

- Baseline conversion rate: 12.04% (control)
- Minimum Detectable Effect (MDE): 2%
- Power: 0.80 | Alpha: 0.05

**Required sample per group: 4,444**
**Available sample per group: ~145,000 ✅**

### 3. Conversion Rates

| Group | Conversion Rate |
|-------|----------------|
| Control (old page) | 12.04% |
| Treatment (new page) | 11.88% |

### 4. Z-Test for Proportions

| Metric | Value |
|--------|-------|
| Z-statistic | 1.3109 |
| P-value | 0.1899 |
| Significance threshold | 0.05 |

**Result: p = 0.1899 > 0.05 → Fail to reject H₀**

---

## Business Conclusion

There is no statistically significant evidence that the new landing page converts differently from the old one. The 0.16% difference observed is within the range of random variation.

**Recommendation: Do not implement the new page.**

Beyond the statistical result, the difference (0.16%) falls well below the 2% MDE defined upfront — meaning even if it were significant, it wouldn't justify the implementation cost.

---

## How We Avoided P-Hacking

| Rule | Applied |
|------|---------|
| Defined H₀ and H₁ before seeing data | ✅ |
| Calculated required sample size before the test | ✅ |
| Used a single primary metric (conversion rate) | ✅ |
| Stopped at the required sample size, not when results looked good | ✅ |

---

## Key Takeaways

- **Statistical significance ≠ business significance.** A massive sample can make tiny, irrelevant differences look significant. The MDE protects against this.
- **P-hacking is subtle.** It doesn't require bad intent — just looking at results before the sample is complete is enough to compromise the test.
- **A valid "no" is as valuable as a "yes".** Not implementing a change that would hurt conversion is a win.

---

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/Cyac221/a-b-testing.git
cd a-b-testing
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv

# Mac/Linux
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the notebook

```bash
jupyter notebook ab_test.ipynb
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| `pandas` | Data cleaning and manipulation |
| `numpy` | Numerical operations |
| `statsmodels` | Z-test for proportions, power analysis |
| `scipy` | Statistical support |
| Jupyter Notebook | Interactive analysis environment |

---

## Data Source

[A/B Testing Dataset](https://www.kaggle.com/datasets/zhangluyuan/ab-testing) — Kaggle
