# When Fair Enough Is Not Enough

Portfolio project on allocation policies and long-term benefit gaps in algorithmic decision-making. The project combines a focused review of 3 long-term fairness perspectives with an empirical employment support simulation using real American Community Survey (ACS) data.

## Overview

Static fairness metrics evaluate a model at one point in time. This project asks a broader question: can a model that appears fair today still produce unequal outcomes after its predictions are converted into decisions and those decisions accumulate over time?

The project brings together three complementary lenses:

- **Policy:** the same risk score can produce different distributions of social goods depending on how it is used.
- **Environment:** repeated interventions change the population and the outcomes the system observes.
- **Time aggregation:** long-term fairness should compare cumulative benefit with cumulative need, rather than only average step-wise gaps.

The final report develops this synthesis in detail. The notebook implements a small empirical simulation inspired by the three papers (it is not an exact reproduction of any single paper).

## References

This project is a survey and empirical synthesis inspired by three papers. The original papers are not redistributed in the portfolio repository, full citations also appear in the final report.

- Policy and the Distribution of Social Goods

**Zezulka, S., and Genin, K. (2024).** “From the Fair Distribution of Predictions to the Fair Distribution of Social Goods: Evaluating the Impact of Fair Machine Learning on Long-Term Unemployment.” *Proceedings of the 2024 ACM Conference on Fairness, Accountability, and Transparency (FAccT '24)*, 1984-2006.
Project connection: motivates evaluating the allocation policy and the downstream distribution of support, rather than treating fairness as a property of the risk score alone.

- Dynamic Labor-Market Environment

**Scher, S., Kopeinik, S., Trügler, A., and Kowald, D. (2023).** “Modelling the Long-Term Fairness Dynamics of Data-Driven Targeted Help on Job Seekers.” *Scientific Reports*, 13.
Project connection: motivates repeated intervention, changing employment probabilities, entry and exit from the active pool, and a timeout rule.

- Long-Term Benefit-Rate Aggregation

**Xu, Y., Deng, C., Sun, Y., Zheng, R., Wang, X., Zhao, J., and Huang, F. (2024).** “Adapting Static Fairness to Sequential Decision-Making: Bias Mitigation Strategies Towards Equal Long-Term Benefit Rate.” *Proceedings of the 41st International Conference on Machine Learning (ICML)*.
Project connection: motivates a ratio-after-aggregation metric that compares cumulative realized benefit with cumulative demand for each group.


## Empirical Experiment

The notebook uses the 2018 ACS 1-Year California person survey through [`folktables`](https://github.com/socialfoundations/folktables). The analysis:

1. Defines an employment prediction task for working-age adults (18-64).
2. Fits a logistic regression model that excludes sex from the predictors.
3. Converts predicted employment probability into unemployment risk.
4. Compares utility-only, demographic-parity, and equal-opportunity allocation rules.
5. Tests high-risk and middle-risk targeting under different capacity levels.
6. Simulates repeated allocation over 12 rounds and 50 random seeds.
7. Evaluates selection, support, job-finding, timeout, and cumulative benefit-rate gaps.

Sex is used as the sensitive attribute in the main experiment. Equal opportunity is treated as an idealized benchmark because observed employment status is used to define need.

## Selected Results

The held-out test set contains 46,942 records. The baseline model reaches 0.735 accuracy and 0.705 ROC AUC. In the dynamic simulation, demographic parity produces the smallest final-round cumulative benefit-rate gaps under both targeting strategies. High-risk targeting produces higher job-finding rates among people needing support and lower post-threshold timeout rates than middle-risk targeting.

| Policy | Targeting | Benefit-rate gap | Selection-rate gap | Job-finding rate among need | Post-threshold timeout rate |
|---|---:|---:|---:|---:|---:|
| Demographic parity | Middle risk | 0.004 | 0.006 | 0.522 | 0.019 |
| Long-term aware | Middle risk | 0.008 | 0.006 | 0.521 | 0.019 |
| Utility only | Middle risk | 0.010 | 0.010 | 0.522 | 0.019 |
| Demographic parity | High risk | 0.013 | 0.006 | 0.548 | 0.010 |
| Long-term aware | High risk | 0.016 | 0.032 | 0.549 | 0.009 |
| Utility only | High risk | 0.019 | 0.035 | 0.549 | 0.009 |

These values describe a stylized simulation, not causal estimates of real program effects. The main takeaway is that allocation rules, targeting choices, and environmental dynamics must be evaluated together (a small selection gap alone does not determine cumulative outcomes).

##Sensitivity and Extensions
Exploratory runs indicate that the magnitude of the simulated outcomes depends on program capacity, intervention effectiveness, the long-term fairness penalty, and the timeout assumption. 
Increasing effective intervention intensity generally raised overall employment, while larger fairness penalties changed the aggressiveness and short-run volatility of the long-term-aware policy. 
These checks were exploratory rather than a controlled robustness analysis, the headline results therefore refer only to the documented baseline configuration.

## Repository Structure

```text
.
├── notebooks/
│   └── 01_long_term_fairness_experiment.ipynb
├── report/
│   ├── BISV_FinalReport.pdf
│   └── Long_Term_Fairness.pdf
├── .gitattributes
├── .gitignore
├── README.md
└── requirements.txt
```

## Data

No source data are committed to this repository. On first execution, the notebook requests the 2018 ACS 1-Year California person survey through `folktables`. The local `data/` cache created by that workflow is excluded from Git.

The analysis therefore requires an internet connection for the initial download. Subsequent runs can use the cached ACS files.

## Environment Setup

Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

Launch JupyterLab:

```bash
jupyter lab
```

Run `notebooks/01_long_term_fairness_experiment.ipynb` from top to bottom. The full dynamic experiment uses parallel processing and can take several minutes depending on the machine.

## Reproducibility Notes

- ACS survey year: 2018
- Geography: California
- Population filter: ages 18-64
- Test split: 20%, stratified by employment outcome
- Random seed for the train/test split: 42
- Dynamic simulation: 12 rounds, 50 seeds, 20% support budget
- Sensitive attribute: sex, excluded from model features
- Cached ACS downloads and notebook checkpoints are ignored by Git

The notebook includes saved outputs for portfolio review. Re-running it will download or read the ACS cache, refit the model, and regenerate the simulation results.

## Results in

- [`notebooks/01_long_term_fairness_experiment.ipynb`](notebooks/01_long_term_fairness_experiment.ipynb): data preparation, model, static policies, capacity analysis, and dynamic simulation.
- [`report/BISV_FinalReport.pdf`](report/BISV_FinalReport.pdf): final paper with literature review, synthesis, methodology, and results.
- [`report/Long_Term_Fairness.pdf`](report/Long_Term_Fairness.pdf): presentation deck.
