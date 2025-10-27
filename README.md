## ⏰ [Live Countdown Timer](https://maximilianBirkle.github.io/Tutorial-Week-8-and-9-Problem-Set-Matching-and-Entropy-Balancing/countdown.html)

---

# Tutorial-Week-8-and-9-Problem-Set-Matching-and-Entropy-Balancing
## 🚚 Deliverables

> Please do this problem set in groups of 2-3. You will have two weeks.  Each group member should hand in an identical copy of the problem set to Canvas.  The problem set should be submitted as a pdf.  You can include answers to the questions up front, with code in the back, or leave the mixed.  The PDF should be clean and readable.  Running your prose through an AI system afterwards is not necessary.
> 

# 📘 Part I: Propensity Scores, Matching, and Robust Post-Matching Inference

> Goal: Estimate the causal effect of an intervention using propensity scores and matching, and perform robust post-matching inference following Abadie & Spiess (2021, JASA).
> 

---

## 🌍 Dataset: UN Interventions and Civil Conflict Duration

**Source:** Michael Gilligan & Ernest Sergenti (2008), *"Do UN Interventions Cause Peace? Using Matching to Improve Causal Inference"*, *Quarterly Journal of Political Science.*

https://nowpublishers.com/article/Details/QJPS-7051

---

## 🎯 Research Question

Do **United Nations interventions** help shorten the duration of civil wars?

Gilligan and Sergenti (2008) use matching methods to re-evaluate earlier findings suggesting that UN interventions *prolong* conflict.

They argue that this conclusion stems from **selection bias** — the UN tends to intervene in the *worst* conflicts.

Matching on pre-treatment covariates helps isolate the causal effect of UN intervention from confounding factors that influence both the **likelihood of intervention** and the **duration of war**.

---

## 📊 Dataset Overview

We will use their replication dataset, `war_pre_snapshots.dta` in the Google Drive.

Each row represents a **conflict episode** observed before a potential UN intervention.

Our goal is to estimate the causal effect of UN involvement (`UN`) on the length of the conflict (`t1 - t0`),

while balancing on key pre-treatment covariates.

---

## ⚙️ Variables

| Variable | Description | Type |
| --- | --- | --- |
| **UN** | Indicator for whether the UN intervened in the conflict (1 = Yes, 0 = No) | Treatment |
| **inter** | Whether the conflict was internationalized | Covariate |
| **deaths** | Number of battle deaths before intervention | Covariate |
| **couprev** | Whether a coup or revolution triggered the conflict | Covariate |
| **sos** | Strategic oil supply (1 = Yes, 0 = No) | Covariate |
| **drugs** | Presence of drug-related economic activity | Covariate |
| **t0** | Starting year of the conflict | Covariate |
| **ethfrac** | Ethnic fractionalization index | Covariate |
| **pop** | Log population of the country | Covariate |
| **lmtnest** | Log of mountainous terrain (proxy for geography and insurgent advantage) | Covariate |
| **milper** | Military personnel per capita | Covariate |
| **eeurop** | Dummy: Eastern Europe region | Covariate |
| **lamerica** | Dummy: Latin America region | Covariate |
| **asia** | Dummy: Asia region | Covariate |
| **ssafrica** | Dummy: Sub-Saharan Africa region | Covariate |

---

## 🧩 Treatment and Covariate Setup

Definte a treatment variable in terms of the `UN`, the outcome is `t1-t0`, and covariates from the remainder of the table in a covariate matrix `X`.

```r
# Treatment variable
treat <- data$UN

# Covariates used in the propensity score model
covar <- c(
  "inter", "deaths", "couprev", "sos", "drugs", "t0",
  "ethfrac", "pop", "lmtnest", "milper",
  "eeurop", "lamerica", "asia", "ssafrica"
)

X <- data[, covar]

```

---

## Q0.  First check of the data

1. Why might UN interventions **not** be randomly assigned across conflicts?
2. Which of the listed variables are most likely to confound the relationship between `UN` and conflict duration?  Run a quick logistic regression and check.

---

### **Q1. Estimating Propensity Scores**

1. Let $T_i \in \{0,1\}$ be the treatment indicator (e.g., whether a unit received an intervention)
    
    and $X_i = (X_{i1}, X_{i2}, \ldots, X_{ip})$ the vector of pre-treatment covariates.
    
    Define the **propensity score**
    $e(X_i) = P(T_i = 1 \mid X_i).$
    
2. Estimate $\hat e(X_i)$ in two ways:
    - **(a)** A **logistic regression** model
    $\text{logit}(e(X_i)) = X_i' \beta.$
    - **(b)** A **random-forest** (or boosting) classifier that predicts $T_i$ from $X_i$,
    obtaining predicted probabilities $\hat e_{\text{RF}}(X_i)$.
3. Report the mean, standard deviation, and range of $\hat e(X_i)$ for treated and control observations.
    
    Create a histogram or kernel density plot of $\hat e(X_i)$ by treatment status.
    

---

## Part B — Matching Design

### **Q2. Implement 1:1 Nearest-Neighbor Matching**

1. Using each estimated propensity score $\hat e(X_i)$ (logit and RF):
    - Match each treated unit to the nearest control on the **logit of the propensity score**:
    $\ell_i = \log\!\left( \frac{\hat e(X_i)}{1 - \hat e(X_i)} \right).$
    - Use **replacement** and a **caliper** of
    $0.2 \times \text{SD}(\ell_i)$
    to restrict poor matches.  The caliper means that if there is no control within the caliper distance of the treated, then we discard that treated observation.  .2 or .1 are standard caliper distances.
    - Keep only matched treated and control units (the ATT sample).
2. Report how many treated units fail to find a match under each design.  How does this change the estimand?

---

## Part C — Balance and Overlap Diagnostics

### **Q3. Standardized Mean Differences (SMDs)**

For each covariate $X^k$:

- **Before matching:**
$\text{SMD}_{\text{raw}}(k) =
\frac{\bar X^k_{T=1} - \bar X^k_{T=0}}
{\sqrt{(s^{2,k})_{T=1}}}.$
    - Please note that this SMD is used when the target is the ATT (i.e. on the treated population).  For an ATE, the denominator of the SMD should be the average of the treated and control,
        
        $\sqrt{\tfrac{1}{2}(s^{2,k}_{T=1} + s^{2,k}_{T=0})}$
        
- **After matching:**
$\text{SMD}{\text{match}}(k) =
\frac{\bar X^{k,\text{treated}}_{\text{match}} -
\bar X^{k,\text{control}}}
{\sqrt{(s^{2,k})_{T=1}}}.$
    - Note that the denominators are the same.
1. Compute SMDs before and after matching for all covariates.
2. Create a *Love plot*, which shows balance before and after matching, by method.  Include logit and RF estimates.  Add a vertical line at 0.1, which is normally considered the acceptable threshold.  You can plot the raw SMD values or their absolute values.
3. Comment on which design achieves better covariate balance.
4. It is common to include not just covariates but also interactions and square terms.  Create two more love plots.  

---

### **Q4. Overlap**

1. For each estimation method, report:
    - $\min$ and $\max$ of $\hat e(X_i)$ for treated and controls.
    - The proportion of treated units whose $$\hat e(X_i)$$ lies inside the support of the controls (and vice versa).
2. Plot the distributions of $\hat e(X_i)$ for treated and controls.
    
    Identify any regions of poor overlap or extreme propensities (near 0 or 1).
    
3. (Optional) Trim observations with $\hat e(X_i)$ outside the common support and re-compute the ATT (report how trimming changes the estimate).
4. Look at the matched subsets—which observations and cases are included.  Do they seem acceptable; does each match seem a fair counterfactual?

---

## Part D — Treatment-Effect Estimation

### **Q5. Matched-Pair ATT**

Let each matched pair be denoted by $(i, j(i))$

where $i$ is treated and $j(i)$ is its matched control.

Compute the **average treatment effect on the treated**:

$\widehat{\tau}_{\text{ATT}} =
\frac{1}{N_T^\ast} \sum_{i \in \mathcal{T}^\ast}
\left( Y_i - Y_{j(i)} \right),$

where $\mathcal{T}^\ast$ is the set of treated units with a valid match.

---

### **Q5.5. Bias–Variance Tradeoff in Matching Ratios**

Nearest-neighbor matching involves a **tradeoff between bias and variance** determined by how many controls are matched to each treated unit.

---

### **(a) Conceptual Question**

For 1-to-$m$ nearest-neighbor matching without replacement, the ATT estimator is

$\widehat{\tau}_{\text{ATT}}^{(m)} = \frac{1}{N_T^\ast} \sum_{i \in \mathcal{T}^\ast}
\Bigg( Y_i - \frac{1}{m}\sum_{j \in \mathcal{J}(i)} Y_j \Bigg),$

where $\mathcal{J}(i)$ is the set of the $m$ closest control matches for treated unit $i$.

1. Explain why increasing $m$ (e.g., from 1 to 2 or 3) tends to:
    - **Decrease variance**, but
    - **Increase bias**.
2. Discuss how this tradeoff relates to:
    - The **distance in covariate space** between matched units, and
3. If overlap is weak (some treated units have few close controls), which risk — bias or variance — dominates as $m$ grows?

---

### **(b) Practical Exercise**

Using your dataset and propensity-score distances:

1. Re-run the matching algorithm for **1:1**, **2:1**, and **3:1** nearest-neighbor matching (with replacement and the same caliper).
    
    Record:
    
    - Number of treated units successfully matched
    - Mean distance between matched pairs
    - The estimated ATT ($\widehat{\tau}_{\text{ATT}}^{(m)}$)
2. Compute **cluster-robust standard errors** for each design using the same procedure as in Q6.
3. Present your results in a small table:

| Matching Ratio | Mean Match Distance | ATT Estimate | Std. Error | N (Matched Pairs) |
| --- | --- | --- | --- | --- |
| 1:1 | … | … | … | … |
| 2:1 | … | … | … | … |
| 3:1 | … | … | … | … |
1. Plot the estimated ATT (y-axis) against $m$ (x-axis).
Add ±1.96×SE error bars.
Comment on whether your results display the expected **bias–variance pattern**.

---

### **(c) Discussion**

- Which design (1:1, 2:1, or 3:1) do you consider most appropriate for this dataset?
- How does the observed pattern relate to theory and to the discussion in **Abadie & Imbens (2006)** on matching bias correction?
- Suppose you had infinite data with perfect overlap — what would happen to this tradeoff?

---

### **Q6. Robust Post-Matching Inference (Abadie & Spiess, 2021)**

Inference on a matched set was, until recently, quite difficult.  Abadie & Spiess, 2021, made a real contribution in showing that one could simply run a regression but then cluster standard errors by each matched set (where each cluster is a the treated observation and its matched controls).  

To implement this, after matching, fit the regression

$Y_i = \alpha + \tau T_i + \varepsilon_i,$

using only the matched data.

Let $s(i)$ denote the **subclass (pair id)** of observation $i$.

Compute **cluster-robust standard errors** for $\hat\tau$ by clustering on $s(i)$.  The equation is below.  For those taking the regression class, we will discuss which errors to put in this estimate (the estimated or leave-one-out).  `HC1` uses the estimated residuals; `HC3` uses the leave-one-out estimates.  I recommend `HC3`, as some simulation evidence shows that they work even under certain types of model misspecification.  You can use R/python to calculate the cluster-robust variance, but for your knowledge, the equation is

$\widehat{V}{\text{CR}}(\hat\tau) =
(X'X)^{-1} \Big( \sum_{s}
X_s' \hat{\varepsilon}_s \hat{\varepsilon}_s' X_s \Big) (X'X)^{-1}$

1. Report $\hat\tau$ and its cluster-robust standard error.
2. Compare results for the logit-matched and RF-matched samples.

---

### **Q7. (Optional) Bootstrap Check**

Bootstraps are, famously, not valid for matching estimators.  Just as a check (optional), implement a **matched-pair bootstrap**:

1. Resample matched pairs (subclasses) with replacement.
2. Recompute $\widehat{\tau}^{(b)}$ for each bootstrap sample $b = 1,\ldots,B$.
3. Report the bootstrap mean, standard deviation, and percentile 95% CI.

Compare these to your cluster-robust results.

Do they tell a similar story?

---

## Part E — Interpretation and Discussion

### **Q8. Reflection**

1. Why does the propensity score $e(X_i)$ act as a **balancing score**?
2. How does random-forest estimation of $e(X_i)$ change the matching results compared to logistic regression?
3. Why is overlap ($0 < e(X_i) < 1$) necessary for identifying the ATT?

---

# 🇩🇪 Part II: Synthetic Control, German Reunification Study

**Dataset:** Available via Harvard Dataverse (doi:10.7910/DVN/24714)

**Paper to read:** *Comparative Politics and the Synthetic Control Method* by Alberto Abadie, Alexis Diamond & Jens Hainmueller (AJPS 2015).

---

## Introduction

In 1990, West Germany underwent reunification with East Germany. The question: *what was the economic cost (or benefit) of this event on West Germany’s GDP per capita?* Using the synthetic control method, the authors construct a counterfactual “synthetic West Germany” from a weighted combination of other OECD countries to estimate the causal effect of reunification.

---

## (a) Conceptual Questions

1. Explain the intuition behind the synthetic control method. What kind of assignment problem is it addressing compared to standard observational designs?
2. Why is it particularly suitable for the West Germany case?
3. What is the key identification assumption for synthetic control to provide a valid causal estimate?

---

## (b) Mathematical/Optimization Questions

1. Write the optimization problem used to select unit weights $w_j$:

$\min_{w} \;\sum_{t \le T_0} \Big(Y_{1t} - \sum_{j=2}^{J+1} w_j Y_{jt}\Big)^2\quad\text{subject to } w_j \ge 0,\; \sum_j w_j = 1.$

Identify each term and explain its meaning.

2. The method also incorporates predictor balancing via selecting $v$-weights over predictors (or moment‑matching). What role do these $v$-weights play and how do they affect the unit weights $w_j$?

3. Explain why the convex‐combination constraint ($w_j\ge0, \sum w_j =1$) is important. What would happen if weights could be negative or sum different than one?

---

## (c) Estimation, Balance Before & After

1. Using the provided dataset, estimate the synthetic control for West Germany (or the designated treated unit) over the pre‑treatment period.
2. Compute a balance table of key predictors (e.g., GDP, trade openness, inflation rate, schooling, investment rate) showing treated mean vs synthetic mean **before treatment**.
3. Report the non-zero weights $w_j$.
4. Interpret: which donor countries dominate the synthetic control and why?
5. Assess whether pre‑treatment fit (balance) is acceptable for credible inference.

---

## (d) Effect Size & Permutation Test

1. Plot the actual trajectory of GDP per capita for West Germany and the synthetic control from pre‑treatment through post‑treatment years. Describe the divergence (if any) after treatment.
2. Calculate the estimated effect (gap) in the first few post‑treatment years and the average post‑treatment gap.
3. Perform a **permutation (placebo) test** by reassigning treatment to each control country and computing the distribution of gaps.
    - Report where the treated unit’s gap falls in that distribution (i.e., approximate p‑value).
4. Interpret: What does the estimated effect size and permutation test suggest about the economic impact of reunification on West Germany?

---

## (e) Placebo Test on Earlier Years

1. Conduct a placebo treatment year **before** the actual 1990 treatment (e.g., 1975) for West Germany. Re‑estimate the synthetic control and plot the gap.
2. What does the behavior of the gap before the true treatment period tell you about the **parallel‑trajectory assumption** or model validity?
3. Based on this placebo pre‑treatment test, comment on how convincing you find the main causal estimate.

---

---

### References

- **Abadie, A., & Spiess, J. (2021).** *Robust Post-Matching Inference.* *Journal of the American Statistical Association.*
- **Rosenbaum, P., & Rubin, D. (1983).** *The Central Role of the Propensity Score in Observational Studies for Causal Effects.* *Biometrika.*
- **Abadie, A., & Imbens, G. (2006).** *Large Sample Properties of Matching Estimators for Average Treatment Effects.* *Econometrica.*
- **Abadie, A., & Gardeazabal, J. (2003).** *The Economic Costs of Conflict: A Case Study of the Basque Country.* American Economic Review, 93(1), 113–132.
- **Abadie, A., Diamond, A., & Hainmueller, J. (2010).** *Synthetic Control Methods for Comparative Case Studies: Estimating the Effect of California’s Tobacco Control Program.* Journal of the American Statistical Association, 105(490), 493–505.
- **Abadie, A., Diamond, A., & Hainmueller, J. (2015).** *Comparative Politics and the Synthetic Control Method.* American Journal of Political Science, 59(2), 495–510.
- **Abadie, A. (2021).** *Using Synthetic Controls: Feasibility, Data Requirements, and Methodological Aspects.* Journal of Economic Literature, 59(2), 391–425.
