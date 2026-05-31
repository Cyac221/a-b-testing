# A/B Testing — Landing Page Conversion Analysis

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
|---|---|
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
|---|---|
| Control (old page) | 12.04% |
| Treatment (new page) | 11.88% |

### 4. Z-Test for Proportions

| Metric | Value |
|---|---|
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
|---|---|
| Defined H₀ and H₁ before seeing data | ✅ |
| Calculated required sample size before the test | ✅ |
| Used a single primary metric (conversion rate) | ✅ |
| Stopped at the required sample size, not when results looked good | ✅ |

---

## How to Run

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd <your-repo-folder>
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

## Requirements

```
asttokens==3.0.1
colorama==0.4.6
comm==0.2.3
debugpy==1.8.20
decorator==5.3.1
executing==2.2.1
ipykernel==7.2.0
ipython==9.14.0
ipython_pygments_lexers==1.1.1
jedi==0.20.0
jupyter_client==8.8.0
jupyter_core==5.9.1
matplotlib-inline==0.2.2
nest-asyncio==1.6.0
numpy==2.4.6
packaging==26.2
pandas==3.0.3
parso==0.8.7
patsy==1.0.2
platformdirs==4.10.0
prompt_toolkit==3.0.52
psutil==7.2.2
pure_eval==0.2.3
Pygments==2.20.0
python-dateutil==2.9.0.post0
pyzmq==27.1.0
scipy==1.17.1
six==1.17.0
stack-data==0.6.3
statsmodels==0.14.6
tornado==6.5.6
traitlets==5.15.0
tzdata==2026.2
wcwidth==0.7.0

```

---

## Key Takeaways

- **Statistical significance ≠ business significance.** A massive sample can make tiny, irrelevant differences look significant. The MDE protects against this.
- **P-hacking is subtle.** It doesn't require bad intent — just looking at results before the sample is complete is enough to compromise the test.
- **A valid "no" is as valuable as a "yes".** Not implementing a change that would hurt conversion is a win.

---

## Tech Stack

- Python 3.x
- pandas, numpy
- statsmodels (proportions_ztest, NormalIndPower)
- Jupyter Notebook
