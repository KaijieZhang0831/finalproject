# Project Proposal

**To:** BST260 Teaching Team, Fall 2026

## Vignette 1: Smoking and body mass index (NHANES)

### Research question and design

Among US adults, does current smoking, compared with never smoking,
lower body mass index (BMI)?

The unit of analysis is the participant, and the population is
non-institutionalized US adults aged 20 to 80 (smoking is only recorded
from age 20). The exposure is current smoker (1) versus never smoker
(0), built from `Smoke100` and `SmokeNow`; former smokers are excluded
because quitting can itself change weight. The outcome is `BMI` (kg/m²),
a continuous variable. The confounders, chosen from subject-matter
reasoning, are age, sex, race/ethnicity (`Race1`), education,
income-to-poverty ratio (`Poverty`) and survey cycle. Because age and
income may relate to smoking and BMI in non-linear ways, a simple linear
adjustment may not be enough.

### Data

The data are NHANES 2009-2010 and 2011-2012, collected by the National
Center for Health Statistics (NCHS) and distributed in the `NHANES` R
package. I use `NHANESraw` (20,293 participants) rather than `NHANES`,
because the 10,000-row `NHANES` table is resampled with replacement and
repeats participants (7,235 adult rows but only 4,654 unique IDs), which
would distort cross-validation. I will use complete cases and report how
many participants are excluded. Descriptive statistics will show the
missingness of each variable, BMI by smoking status within age groups,
and the confounders by smoking status.

### Methods

I will start with the crude difference in mean BMI and a multiple linear
regression of BMI on smoking and the confounders. The main estimate
comes from the same regression with smooth (spline) terms for age and
income.

Machine learning will check whether this adjustment is adequate. Using
`caret` with cross-validation, I will compare how well linear
regression, k-nearest neighbors and random forests predict BMI from the
confounders. If the flexible models predict clearly better, a linear
adjustment is likely not enough. If time permits, I will attempt double
machine learning and a causal forest.

### Limitations

The data are cross-sectional, so reverse causation is possible, for
example weight loss from illness. Smoking is self-reported as yes/no,
with no information on how much people smoke, and unmeasured confounders
such as diet may remain.

## Vignette 2: Permitless carry laws and firearm mortality

### Research question and design

Did adopting a permitless carry law change a state’s firearm death rate?

The unit of analysis is the state-year, covering the 50 states and D.C.
from 2014 to 2024. The exposure is whether a permitless carry law is in
force, starting from its first full calendar year. Twenty-three states
adopted such a law between 2016 and 2024; four states that adopted
before 2014 are excluded. The outcome is the log age-adjusted firearm
death rate, a continuous variable. State and year fixed effects account
for stable differences between states and for national trends. The key
assumption is parallel trends: without the law, adopting and
non-adopting states would have followed similar trends.

### Data

`gunleg` is the RAND Corporation State Firearm Law Database
(<https://www.rand.org/pubs/tools/TLA243-2-v4.html>), with 1,902 records
of state firearm laws, their type and effective date. Permitless carry
laws are the records with subtype “shall issue (permit not required)”;
Texas’s 2021 law is coded “Restrictive” unlike every other state’s, so I
define the exposure by subtype. `cdcfiremort` holds CDC/NCHS
age-adjusted firearm death rates per 100,000 by state for 2005 and
2014-2024 (<https://www.cdc.gov/nchs/state-stats/deaths/firearms.html>).
The firearm death rate has no missing values for 2014-2024. Descriptive
statistics will show when each state adopted the law and firearm death
rates over time for adopting and non-adopting states.

### Methods

The main analysis is a regression of the log firearm death rate on a law
indicator with state and year fixed effects, with standard errors that
allow for correlation within each state. To check the parallel trends
assumption, I will add indicators for each year before and after
adoption (an event study); the estimates before adoption should be near
zero.

Because states adopted in different years, a single regression can be
misleading. I will also compare each group of states that adopted in the
same year with states that had not yet adopted, and average these
comparisons. If time permits, I will try a recently developed
difference-in-differences estimator designed for staggered adoption.

### Limitations

Adopting states may differ from other states in ways that change over
time, which would violate parallel trends. The data are state-level and
annual, and the outcome combines firearm suicides and homicides.
