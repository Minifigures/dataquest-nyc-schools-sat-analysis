# What Predicts a School's Average SAT Score in New York City?

Dataquest guided project, *Analyzing NYC High School Data*. Data: New York City Department of Education, 2011-12. SAT results, demographics, class sizes and AP participation joined on the school identifier (DBN), covering 412 high schools with a reported SAT average.

## What this analysis can and cannot be

Every number here is a **school-level aggregate**. A row is a school, not a student. A correlation between a school's composition and its mean score describes schools and says nothing about any individual. Treating an aggregate relationship as if it held for individuals is the ecological fallacy, and it is the easiest way to turn this dataset into a wrong and harmful claim.

There is also no randomisation here. NYC high schools admit by screening, audition, lottery and, for eight of them, a citywide entrance exam. Schools differ in who applies and who is admitted before any teaching happens, so **nothing in this analysis is causal.**

The worthwhile question is narrower: the raw correlations get reported from this dataset routinely, so **how many of them survive the most obvious control?**

## Findings

### 1. Poverty is the strongest single correlate

`frl_percent`, the free-lunch eligibility rate, correlates **-0.721** with a school's mean SAT score, ahead of every demographic composition variable (`white_per` +0.652, `asian_per` +0.545, `hispanic_per` -0.363, `black_per` -0.310). In a segregated school system, school poverty and school racial composition are themselves heavily correlated, so these are largely the same fact reported several times.

### 2. Holding poverty constant: a mixed result

| Variable | Raw | Partial | Change |
|---|---|---|---|
| hispanic_per | -0.364 | **+0.052** | collapses to nothing |
| ell_percent | -0.380 | -0.129 | down 66% |
| white_per | +0.647 | +0.288 | down 56% |
| avg_class_size | +0.396 | +0.193 | down 51% |
| total_enrollment | +0.386 | +0.201 | down 48% |
| sped_percent | -0.421 | -0.343 | down 19% |
| asian_per | +0.552 | +0.455 | down 18% |
| black_per | -0.303 | **-0.386** | **gets stronger** |

The `hispanic_per` association disappears entirely: it was school poverty under another name. The English-learner association falls by two thirds, and what remains is unsurprising given that the SAT is administered in English.

**The `black_per` association strengthens under the control.** A tidy "it is really just poverty" narrative would omit that line. Free-lunch eligibility is a crude threshold measure of family income, and the residual association measures what that single control fails to capture across these schools. It is not evidence about students.

**The overlap is the real finding.** Poverty alone explains 51.6% of the variance in school mean scores; the four racial shares alone explain 56.2%; together they reach only 65.2%. Two sets of variables that would account for 108% between them if independent are describing largely the same segregation.

### 3. Class size: a trap the brief walks into

Average class size correlates **positively** with SAT scores (+0.396). Nobody believes crowding raises scores. Holding poverty constant halves it to +0.193, and the remainder still cannot be read causally: large schools in New York are disproportionately the long-established selective ones, so this measures which schools are big, not what class size does.

**The honest answer to "does class size affect SAT scores" from this data is that the question cannot be answered with it.**

### 4. The eight exam schools do not explain the pattern

The SHSAT specialized high schools are a striking illustration of selection: **1.9% of the city's high schools, averaging 4.03 standard deviations above the rest** (1,903 against 1,196), holding 7 of the top 10 scores, with a 31.3% poverty rate against 66.8% elsewhere.

Remove all eight and the correlations barely move. The largest change is `asian_per`, 0.545 to 0.442; `white_per` rises slightly. **The pattern is citywide**, a more uncomfortable finding than "a few unusual schools skew the data", and the reason the check was worth running rather than assuming.

(Matching used exact DBNs. A substring search for "STUYVESANT" also catches Bedford Stuyvesant Preparatory, an unrelated and very differently situated school, which an earlier pass did include.)

## Method

1. Join SAT results, demographics (2011-12), general-education grade 9-12 class sizes and AP participation on DBN, building the class-size DBN from district plus school code.
2. Sum SAT sections with `min_count=3` so a school missing a section gets NaN rather than a misleadingly low total.
3. Partial correlation: regress both the score and the variable on `frl_percent` and correlate the residuals.
4. OLS via `numpy.linalg.lstsq`, so there is no dependency beyond pandas and NumPy.

## What this cannot support

- **School aggregates describe schools, not students.** Nothing here supports a claim about any student or group of students.
- **Nothing is causal.** No variable separates what a school does from who it enrols.
- **The SAT is a selected sample within each school,** being the average among students who chose to sit it.
- **57 of 478 schools report no score.** NYC suppresses results below five test takers, so the smallest schools are missing by design.
- **`frl_percent` is a benefit-eligibility threshold, not income.**
- **One school year, 2011-12,** on a test since redesigned.

## What would actually answer the question

Student-level data with prior achievement, supporting a value-added comparison: how much do students at a school gain relative to similar students who started in the same place? That is what distinguishes a good school from a selective one, and it is not in this dataset.

## Files

- `notebook.ipynb`: full analysis, executed, with two charts.

## Stack

pandas, NumPy, Matplotlib (fivethirtyeight style). No seaborn.