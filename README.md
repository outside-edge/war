## WAR Scores 

WAR stands for **Wins Above Replacement**. Originally developed in baseball, it quantifies how many more wins a player contributes relative to a "replacement-level" player — a hypothetical freely available, average substitute. In cricket, WAR can be adapted to estimate how many wins a batter contributes over a replacement-level batter, based on their run-scoring ability.

### 1. Naive Batting WAR

The naive approach to Batting WAR assumes all run-scoring opportunities are equal. It focuses purely on aggregate statistics like total runs, strike rate, and balls faced.

#### Formula:

Let:

* $R_i$: total runs scored by player $i$
* $B_i$: balls faced by player $i$
* $SR_{rep}$: replacement-level strike rate (e.g., 20th percentile of observed SRs)
* $R_{rep, i} = B_i \times \frac{SR_{rep}}{100}$: expected runs for replacement-level batter

Then:
$WAR_i^{naive} = \frac{R_i - R_{rep, i}}{10}$

Here, 10 runs are heuristically assumed to be worth 1 win in ODI cricket.

### 2. Context-Adjusted Batting WAR

The naive model assumes equal opportunity for all balls, which is unrealistic. Batters face vastly different conditions depending on match context (e.g., powerplay vs death overs, era, batting position).

To address this, we model **expected runs per ball** as a function of context using a **Generalized Additive Model (GAM)**.

#### Features used:

* $t$: over number (continuous)
* $p$: inferred batting position (categorical)
* $y$: year (categorical)

#### Model:

$\mathbb{E}[r_{i,j}] = f_1(t_{i,j}) + f_2(p_{i,j}) + f_3(y_{i,j})$
Where:

* $r_{i,j}$: runs scored by player $i$ on ball $j$
* $f_1$: smooth spline on over
* $f_2, f_3$: factor effects for position and year

#### Replacement Adjustment:

To define a replacement-level expectation, we scale the predicted expected runs:
$R^{rep}_{i} = \alpha \cdot \sum_j \hat{r}_{i,j} \quad \text{where } \alpha < 1$
(e.g., $\alpha = 0.85$)

#### Context-Adjusted WAR:

$WAR_i^{context} = \frac{\sum_j r_{i,j} - R^{rep}_{i}}{10}$

### Summary

| Approach    | Accounts for Context? | Replacement Defined By             | Model Used             |
| ----------- | --------------------- | ---------------------------------- | ---------------------- |
| Naive WAR   | No                    | Fixed strike rate (percentile)     | None (formulaic)       |
| Context WAR | Yes                   | GAM-predicted runs $\times \alpha$ | GAM (spline + factors) |

Context-adjusted WAR is a more robust, fair comparison of batting value — especially across roles, eras, and match phases.
