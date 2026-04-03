# Plan For Additional Fixed Effects Visuals

## Goal

Add a small set of high-value visuals to `fixed_effects_demo.ipynb` that make the mechanics of fixed effects visually concrete for students, not just algebraic.

The new visuals should help students answer three questions:

1. What does an **entity fixed effect** remove from the data?
2. What does a **time fixed effect** remove from the data?
3. What is left over after demeaning, and why is that the variation used to estimate coefficients?

## Design Principles

- Prefer visuals that show a **before/after transformation** rather than only a final regression result.
- Use **simple geometry**: shifting, centering, and residual variation are easier to teach than abstract matrix notation.
- Keep the new material tightly connected to the existing notebook sections on within-vs-between variation and two-way fixed effects.
- Use a mix of **real REIT data** and **small synthetic teaching data** only where the real data is too noisy to show the idea cleanly.
- Make the visuals answer a specific teaching sentence, not just add another chart.

## Proposed Additions

### Visual 1: Entity Fixed Effects As Firm-Specific Recentering

**Concept to teach:** Entity fixed effects subtract each firm's own average level, so the model no longer compares high-level firms to low-level firms. It compares each firm to itself.

**Recommended placement:** Immediately after the existing within-vs-between section and before the first fixed-effects model.

**Data source:** Real REIT data, restricted to 3 to 5 highlighted firms with enough time observations.

**Chart design:** Two-panel figure.

- Left panel: raw `lnmcap` over time for a few firms.
  - Each firm's line sits at a different average level.
  - Add a dashed horizontal line for each firm's mean.
  - Teaching point: these average differences are the firm fixed effects.
- Right panel: demeaned `lnmcap` over time, where each point is `lnmcap_it - mean(lnmcap_i)`.
  - Same firms, same years, same colors.
  - All firm-specific mean lines collapse to zero.
  - Teaching point: after entity demeaning, each firm is centered around zero, and only within-firm movement remains.

**Why this works:**

- Students can literally see the vertical recentering.
- It avoids making fixed effects feel like a black-box regression option.
- It connects directly to the notebook's existing discussion of the within transformation.

**Possible annotation text inside the notebook:**

"Entity FE does not change the timing of a firm's ups and downs. It removes the firm's average level so the model uses only deviations from that firm's own norm."

### Visual 2: Demeaning In Scatter-Space

**Concept to teach:** Entity FE changes the regression question from levels to deviations from entity means.

**Recommended placement:** Right after Visual 1.

**Data source:** Real REIT data for the same highlighted firms.

**Chart design:** Two-panel scatter with arrows or matched points.

- Left panel: raw scatter of `lnmcap` vs `ret12` for a few firms.
  - Show each firm's centroid `(mean lnmcap_i, mean ret12_i)`.
  - Optionally connect each observation to its centroid with faint line segments.
- Right panel: demeaned scatter of `(lnmcap_it - mean lnmcap_i)` vs `(ret12_it - mean ret12_i)`.
  - All centroids now sit at `(0, 0)`.
  - Add horizontal and vertical zero lines.

**Why this works:**

- Students often understand line plots but still miss what regression is doing.
- This visual shows the same transformation in the exact space where the regression slope is estimated.
- It makes the phrase "the firm fixed effect is absorbed into the intercept" concrete: the firm-specific centers disappear.

**Implementation note:**

If the real-data version is too cluttered, switch to a tiny synthetic example with 3 firms and 4 periods so arrows from raw points to firm centroids are easy to see.

### Visual 3: Time Fixed Effects As Year-Specific Common Shock Removal

**Concept to teach:** Time fixed effects remove shocks common to all firms in a given year, such as recessions or booms.

**Recommended placement:** At the start of the two-way fixed-effects section, before estimating the model.

**Data source:** Real REIT data.

**Chart design:** Two-panel figure.

- Left panel: yearly average return across all firms.
  - Plot the cross-firm mean of `ret12` by year.
  - Highlight major shock years if they stand out.
  - Teaching point: this common movement is what year fixed effects absorb.
- Right panel: firm returns after subtracting the year mean, `ret12_it - mean(ret12_t)`.
  - Show a sample of firm trajectories or a distribution by year.
  - Add a zero line.
  - Teaching point: after year demeaning, each year's common level is removed.

**Why this works:**

- Students usually grasp firm heterogeneity faster than time heterogeneity.
- This figure frames time FE as removing macro conditions shared by everyone in that year.
- It provides an intuitive bridge from one-way FE to two-way FE.

**Possible annotation text inside the notebook:**

"A time fixed effect is a year-specific intercept. It shifts all firms up or down together for that year, then removes that common shift before estimating slopes."

### Visual 4: Synthetic Two-Way FE Decomposition Diagram

**Concept to teach:** Two-way FE removes both a firm effect and a year effect, leaving a doubly-demeaned residual.

**Recommended placement:** Immediately after Visual 3, as a compact conceptual diagram.

**Data source:** Small synthetic panel with 3 firms and 4 years.

**Chart design:** A simple table-style visual or heatmap-like matrix.

- Create outcome values from:
  - firm baseline effect
  - year shock
  - small idiosyncratic residual
- Show three aligned panels:
  - raw values
  - after removing firm means
  - after removing firm and year means

Alternative presentation:

- A decomposition table that labels each cell as:
  - raw value
  - minus firm effect
  - minus year effect
  - equals remaining within-firm, within-year variation

**Why this works:**

- Real data are valuable, but synthetic data can reveal the mechanism with zero ambiguity.
- This gives a clean visual explanation of what two-way FE means mathematically.
- It is especially useful for students who struggle to infer the transformation from noisy empirical plots.

## Recommended Final Scope

Add **three** visuals to the notebook, with one optional fourth if the flow still feels clean.

Recommended minimum set:

1. Entity FE recentering line plot using real data.
2. Time FE common-shock removal plot using real data.
3. Small synthetic two-way FE decomposition diagram.

This combination is better than adding only real-data charts because:

- real data show authenticity,
- synthetic data show mechanism,
- together they address both intuition and interpretation.

## Notebook Integration Plan

### In Section 3: Within vs. Between Variation

Insert one new subsection titled something like:

`### Visualizing What Entity Fixed Effects Actually Remove`

Add:

- a short markdown explanation of demeaning,
- Visual 1,
- a brief interpretation cell.

### In Section 6: Model 3 — Two-Way Fixed Effects

Insert one new subsection before the model estimation cell titled something like:

`### Visualizing What Time Fixed Effects Remove`

Add:

- a short markdown explanation of common yearly shocks,
- Visual 3,
- optionally Visual 4 immediately after it.

## Implementation Notes

- Reuse the notebook's existing color constants so the new visuals feel native to the document.
- Keep labels explicit and beginner-friendly: say "firm average" and "deviation from firm average," not just "demeaned."
- Save exported figures with names consistent with the current convention, for example:
  - `fe_demo_entity_demeaning_lines.png`
  - `fe_demo_time_effect_demeaning.png`
  - `fe_demo_twoway_fe_synthetic_diagram.png`
- Add short interpretation text after each figure so students are told exactly what to notice.

## Success Criteria

The added visuals are successful if a student can explain, in plain language:

1. Entity FE subtracts each firm's own average, so the model compares a firm to itself.
2. Time FE subtracts each year's common shock, so the model ignores economy-wide year shifts.
3. Two-way FE estimates slopes from the variation left after both of those average components are removed.

## Recommended Next Step

Implement the minimum set of three visuals first, then run the notebook end-to-end to confirm the figures are readable and the teaching sequence still feels smooth.