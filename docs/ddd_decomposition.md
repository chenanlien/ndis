# Why the Aggregate DDD Can Trend Downward Even When Subgroup DDDs Do Not

A useful way to think about the aggregate DDD is:
$$
DDD_{\text{all},e}
=
\sum_a w^H_{a,e} DiD^H_{a,e}
-
\sum_a w^L_{a,e} DiD^L_{a,e},
$$

where:

- \(a\) indexes child-age groups,
- \(H\) denotes the high-risk group,
- \(L\) denotes the low-risk group,
- \(e\) denotes event time,
- \(w^H_{a,e}\) and \(w^L_{a,e}\) are the weights placed on each child-age group.

The important point is that the aggregate DDD is **not generally a weighted average of the child-age-specific DDDs**.

For a given child-age group,

\[
DDD_{a,e}
=
DiD^H_{a,e}
-
DiD^L_{a,e}.
\]

A weighted average of subgroup DDDs would instead look like:

\[
\sum_a w_{a,e}
\left(
DiD^H_{a,e}
-
DiD^L_{a,e}
\right),
\]

which implicitly requires the high- and low-risk groups to use the same composition weights.

## Simple example

Suppose there are only two child-age groups: **Young** and **Old**.

Assume that within each child-age group, the high-risk and low-risk CSDiD paths are identical:

| Event time | Young: High | Young: Low | Old: High | Old: Low |
|---|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 | 0 |
| 1 | 10 | 10 | -10 | -10 |
| 2 | 20 | 20 | -20 | -20 |

Therefore, within each child-age group:

\[
DDD_{\text{Young}} = 0
\]

and

\[
DDD_{\text{Old}} = 0.
\]

So neither subgroup DDD has any downward trend.

Now suppose the child-age composition differs substantially between the high- and low-risk groups:

- High risk: 20% Young, 80% Old
- Low risk: 80% Young, 20% Old

At event time \(e=1\),

\[
DiD^H
=
0.2(10)+0.8(-10)
=
-6,
\]

while

\[
DiD^L
=
0.8(10)+0.2(-10)
=
6.
\]

Therefore,

\[
DDD_{\text{all}}
=
-6-6
=
-12.
\]

At event time \(e=2\),

\[
DiD^H
=
0.2(20)+0.8(-20)
=
-12,
\]

and

\[
DiD^L
=
0.8(20)+0.2(-20)
=
12.
\]

Thus,

\[
DDD_{\text{all}}
=
-12-12
=
-24.
\]

The result is:

| Event time | Young DDD | Old DDD | Aggregate DDD |
|---|---:|---:|---:|
| 0 | 0 | 0 | 0 |
| 1 | 0 | 0 | -12 |
| 2 | 0 | 0 | -24 |

So **every within-age DDD can be flat, while the aggregate DDD displays a strong downward trend**.

## Decomposition

The aggregate expression can also be rewritten using the low-risk weights as a reference:

\[
DDD_{\text{all},e}
=
\sum_a w^L_{a,e}
\left(
DiD^H_{a,e}
-
DiD^L_{a,e}
\right)
+
\sum_a
\left(
w^H_{a,e}
-
w^L_{a,e}
\right)
DiD^H_{a,e}.
\]

The first term is a weighted average of the within-child-age DDDs:

\[
\sum_a w^L_{a,e}DDD_{a,e}.
\]

The second term is a **composition term**:

\[
\sum_a
\left(
w^H_{a,e}
-
w^L_{a,e}
\right)
DiD^H_{a,e}.
\]

Therefore, even if all within-child-age DDDs are close to zero, the aggregate DDD can still move substantially if:

1. the high- and low-risk groups have different child-age compositions; and
2. the underlying CSDiD paths differ across child-age groups.

In the empirical application, this is particularly relevant because the child-age composition of high- and low-risk mothers differs substantially within the same parent-age group.

In addition, because this is a staggered-adoption design, the effective weights may vary with event time:

\[
w^H_{a,e},\qquad w^L_{a,e}.
\]

Different cohorts contribute at different event times, so changes in cohort support can create an additional source of variation in the aggregate path.

## Interpretation

The key distinction is:

> The pooled DDD is a difference between two differently weighted mixtures, not a simple weighted average of the within-child-age DDDs.

Therefore, when the aggregate DDD trends downward but the child-age-specific DDDs do not, the next diagnostic is to separate:

- **within-child-age high-versus-low differences**, and
- **differences in child-age composition between the high- and low-risk groups**.

A useful next step is to construct a common-composition or standardized DDD that forces the high- and low-risk groups to use the same child-age weights.
