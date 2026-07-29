# Enterprise Delivery Performance

**Does investing in test automation actually improve delivery reliability — and where does it fail to?**

A sprint-delivery analytics project built in Power BI, analysing 124 sprints across 6 software projects over 12 months.

---

## ⚠️ Read this first: the data is synthetic and I authored it

I built this dataset myself. I decided which teams adopted automation and when, and I deliberately designed one adopter to gain nothing because its planning stayed inflated.

**The numbers below are properties of a simulation, not evidence about the real world.** I'm stating that plainly rather than dressing it up as a discovery.

What's real is the *analytical design*: the control group, the adoption-date anchoring, grouping teams by an input rather than an outcome, and testing alternative explanations before claiming anything. Those choices are what this project demonstrates, and they'd hold up on real data.

---

## The question

Delivery dashboards usually answer *"what happened."* Far fewer answer *"did our investment cause it — and where didn't it?"*

Test automation isn't new; it's been standard practice for two decades. The less-examined question is when it **fails** to pay off. So this project is built around that: not "does automation work," but "under what conditions does it not?"

---

## Key results

| Metric | Before automation | After | Change |
|---|---|---|---|
| Sprint completion (disciplined adopters) | 77.94% | 93.26% | **+19.7%** |
| Defects per sprint | 9.5 | 4.5 | **−53%** |
| High-severity defects per sprint | 2.58 | 0.50 | **−81%** |
| Rework hours per sprint | 56.3 | 22.7 | **−60%** |
| SLA breach rate | 21.1% | 11.5% | **−45%** |
| **Control group (3 non-adopters)** | **82%** | **82%** | **flat** |

The control group row is the one that matters. Without it, "we improved after adoption" could just mean teams got better with practice over the year.

### The case built in deliberately

One adopter received the same tooling and gained nothing:

| | Completion | SLA breach | Planned pts/sprint |
|---|---|---|---|
| Disciplined adopters | 77.94% → **93.26%** | 15.56% | 96.8 – 97.6 |
| **HR Workflow (overcommitting)** | 78.12% → **77.71%** | **36.84%** | **109.3** |
| Control group | 82% (flat) | 16.67% | 93.5 – 99.1 |

HR planned 109 story points per sprint while delivering around 85 — roughly 22% sustained overcommitment. Automation returns time to a team. If that time is already promised away, nothing is left to keep.

**Planning discipline isn't a companion to automation. It's the precondition.**

An analysis reporting only the average across adopters would miss that team entirely. In real work, that's usually where the useful answer lives.

---

## The design decisions that did the work

**1. A control group.** Three projects never adopted automation and were tracked over the same period. If completion rose everywhere, the improvement wouldn't be about the tooling. It didn't — they stayed flat at 82%.

**2. Pre/post anchored to each project's own adoption date.** Adoption happened on 15 May, 1 June and 1 July for different projects. A single shared cut-off date would misclassify sprints, so every sprint is compared against *its own* project's date.

**3. Teams grouped by an input, never an outcome.** Groups are defined by **average planned story points per sprint** — a planning decision made before any work happens. Grouping by completion rate instead would mean sorting teams by their results and then reporting that they had different results, which is circular.

**4. Alternative explanations tested.** Team size was the obvious candidate driver. Across teams of 6, 7, 8 and 11 the completion rates were 84.8%, 82.1%, 82.1% and 81.9% — a 3-point spread across a near-doubling of team size. No relationship. A null result reported is part of the analysis, not a gap in it.

---

## Dashboard

### Page 1 — Delivery Performance
*Does automation work?*

<img width="1672" height="941" alt="Enterprise_Delivery_Performance_Dataset 1" src="https://github.com/user-attachments/assets/f0037604-4b21-4878-b153-1fe8bed708ea" />

KPI strip, adopters-vs-control trend, planning volume vs completion, project ranking, and the before/after comparison.

### Page 2 — Quality & Automation Impact
*What else changed?*

<img width="1672" height="941" alt="Enterprise_Delivery_Performance_Dataset 2" src="https://github.com/user-attachments/assets/3a414dd0-19d0-488a-8908-c4d1a93ee031" />

Defect reduction by severity, deployment success by coverage, SLA breach by planning group, and team size ruled out as a driver.

Colour is semantic throughout: **blue** = adopted with realistic planning, **grey** = control group, **red** = the overcommitting adopter. The palette deliberately avoids red/green, the most common colour-blindness pairing, since those two series carry the main finding.

---

## Metric definitions

| Metric | Definition |
|---|---|
| Completion Rate | `SUM(Completed_Story_Points) / SUM(Planned_Story_Points)` |
| Overcommitment % | `1 − Completion Rate` (see Limitations — it is not independent) |
| Sprint Success Rate | % of sprints reaching ≥ 90% completion |
| Defects per Sprint | `COUNT(Defects) / COUNT(DISTINCT Sprint_ID)` |
| Rework Hours per Sprint | `SUM(Rework_Hours) / COUNT(DISTINCT Sprint_ID)` |
| SLA Breach Rate | `SUM(SLA_Breached) / COUNT(SLA rows)` |
| Deployment Success Rate | `SUM(Successful) / COUNT(Deployments)` |
| Automation Phase | Before / After, per sprint, vs that project's own adoption date |
| Adoption Group | Never Adopted · Adopted-Realistic (avg planned ≤ 105) · Adopted-Overcommitting (> 105) |

---

## Limitations

Stated up front, because an analysis that hides these is worth less than one that names them.

- **The dataset is synthetic and self-authored.** The effect sizes are designed in. This project demonstrates analytical structure, not empirical findings.
- **Overcommitment % is not independent of completion rate** — it's defined as `1 − completion`, so a scatter of one against the other is arithmetic, not a finding. The dashboard therefore plots *average planned story points* (a genuine input) against completion instead.
- **Planning volume vs completion is r ≈ −0.60 across only 6 projects**, driven substantially by one outlier. Suggestive, not conclusive.
- **Only one project sits in the low-discipline group.** The correct claim is "one adopter showed no gain," never "low-discipline teams show no gain."
- **Deployment success by coverage is confounded with time** — high coverage only exists after adoption, so the comparison can't separate the two.
- **The SLA chart is about planning, not automation.** Adopters (15.56%) and non-adopters (16.67%) are effectively identical; the overcommitting team (36.84%) is the outlier. The automation-SLA effect only appears in the before/after within adopters.
- **Correlation is not causation.** The control group is what carries the causal argument here, not the 0.58 correlation between coverage and completion.

---

## Data model

Six tables, star-shaped around sprint-level grain:

| Table | Rows | Grain |
|---|---|---|
| Projects | 6 | one row per project |
| Sprints | 124 | one row per sprint (14-day cadence) |
| Defects | 930 | one row per defect |
| Deployments | 251 | one row per deployment |
| SLA | 124 | one row per sprint |
| Automation | 124 | automation coverage % per sprint |

`Projects[Project_ID]` → one-to-many → every other table.

---

## Repository structure

```
├── README.md
├── data/
│   └── Enterprise_Delivery_Performance_Dataset.xlsx   # 6 tables, 124 sprints
└── images/
    ├── page1-delivery-performance.png
    └── page2-quality-automation-impact.png
```

The dataset is included so every figure above can be independently checked.

---

## Coming next

- **SQL layer** — a T-SQL script reproducing each headline number from the raw tables, so the analysis is verifiable outside Power BI
- **.pbix file** — the Power BI source, once the SQL version is in place

---

## Built with

Power BI Desktop — DAX measures, calculated columns, conditional formatting, semantic colour encoding.

---

*Built by Divyansh Goyal*
