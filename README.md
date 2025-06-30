## Cricket WAR

WAR stands for **Wins Above Replacement**. Originally developed in baseball, it quantifies how many more wins a player contributes relative to a "replacement-level" player — a hypothetical freely available, average substitute. In cricket, WAR can be adapted to estimate how many wins a player contributes over replacement based on run-scoring (batting) or run-prevention (bowling).

We construct both **batting WAR** and **bowling WAR**, and split all calculations by gender (men's and women's ODIs), acknowledging the substantial differences in scoring patterns and match contexts.

---

### 1. Naive Batting WAR

The naive approach assumes all run-scoring opportunities are equal. It focuses purely on aggregate statistics like total runs, strike rate, and balls faced.

**Formula:**
Let:

* \$R\_i\$: total runs scored by player \$i\$
* \$B\_i\$: balls faced by player \$i\$
* \$SR\_{rep}\$: replacement-level strike rate (e.g., 20th percentile of observed strike rates)
* \$R\_{rep, i} = B\_i \times \frac{SR\_{rep}}{100}\$: expected runs for replacement-level batter

Then:

$$
WAR_i^{naive} = \frac{R_i - R_{rep, i}}{10}
$$

Here, we assume **10 runs = 1 win**, a simplifying normalization.

---

### 2. Context-Adjusted Batting WAR

Context matters: players bat in different overs, roles, and eras. To estimate performance fairly, we model expected runs per ball as a function of context using a **Generalized Additive Model (GAM)**.

**Features used:**

* \$t\$: over number (continuous)
* \$p\$: inferred batting position (categorical)
* \$y\$: year (categorical)

**Model:**

$$
\mathbb{E}[r_{i,j}] = f_1(t_{i,j}) + f_2(p_{i,j}) + f_3(y_{i,j})
$$

Where:

* \$r\_{i,j}\$: runs scored by player \$i\$ on ball \$j\$
* \$f\_1\$: smooth spline on over
* \$f\_2, f\_3\$: factor effects for position and year

**Replacement Adjustment:**
To define a replacement-level player, we scale down the predicted expected runs:

$$
R^{rep}_{i} = \alpha \cdot \sum_j \hat{r}_{i,j}, \text{ with } \alpha = 0.85
$$

**Contextual WAR:**

$$
WAR_i^{context} = \frac{\sum_j r_{i,j} - R^{rep}_{i}}{10}
$$

---

### 3. Bowling WAR

Bowling WAR focuses on **run prevention**. For each bowler, we compute their actual runs conceded and compare it to what a replacement-level bowler would have conceded in the same number of balls.

**Steps:**

1. Aggregate:

   * Balls bowled
   * Runs conceded
2. Compute:

   * Bowler’s runs per ball (RPB)
   * Replacement RPB: 80th percentile of all RPBs
   * Expected replacement runs: \$\text{balls} \times \text{replacement RPB}\$

**Bowling WAR Formula:**

$$
WAR_i^{bowling} = \frac{\text{replacement\_runs}_i - \text{actual\_runs}_i}{10}
$$

As with batting, we assume 10 runs = 1 win. This is a simplification that normalizes scale.

---

### Summary

| Metric          | Context-Aware | Replacement Defined By               | Model Used             | Gender Split? |
| --------------- | ------------- | ------------------------------------ | ---------------------- | ------------- |
| Naive Batting   | No            | Fixed strike rate percentile         | None (formulaic)       | Yes           |
| Context Batting | Yes           | GAM-predicted runs \$\times \alpha\$ | GAM (spline + factors) | Yes           |
| Bowling WAR     | No            | Percentile of runs-per-ball          | None (formulaic)       | Yes           |

### Data

We use data from [https://cricsheet.org/downloads/](https://cricsheet.org/downloads/) and parse individual YAMLs to compute WAR metrics separately for men and women, ensuring fair comparisons across formats.

