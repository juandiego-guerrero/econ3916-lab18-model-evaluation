# econ3916-lab18-model-evaluation
Lab 18 Submission

# Model Evaluation: The Curve, and What It Cannot Tell You

## Objective
I compared two classifiers on a rare outcome using ROC and precision-recall curves, a paired bootstrap and an expected-cost curve, to show that a single summary score cannot pick a model or a threshold on its own.

## Methodology
- Simulated 8,000 cases of a rare outcome from a known logistic formula and split them 70/30, stratified so both sets keep the same share of positives.
- Fitted a scaled logistic regression and drew its ROC curve and precision-recall curve on the test rows, with ROC AUC and average precision as the summary scores.
- Fitted a gradient-boosted tree model and compared both models' AUC, average precision, and precision at four recall levels.
- Ran a paired bootstrap (500 resamples of the test set, scoring both models on each resample) to put a 95% interval on the gap between them.
- Built an expected-cost curve with a missed positive costing ten times a false alarm, chose the cheapest threshold on cross-validated training probabilities, and priced it once on the test rows.
- Swept the cost ratio from 2:1 to 50:1 for both models, choosing each threshold the same way, to see whether the cheaper model changes.
- Wrote `roc_point()`, a function that returns the false positive rate and true positive rate for any model, rows and threshold.

## Key Findings
- 11.8% of the test rows were positive (283 of 2,400).
- The logistic model's ROC AUC was 0.847, but its average precision was only 0.465. AUC ignores how rare positives are, while precision shows how many false alarms come with each true positive.
- The boosted model's ROC AUC was 0.8431. The AUC difference on the full test set was +0.0038, and the paired bootstrap gave a 95% interval of [−0.0020, +0.0094]. The interval includes zero, so this test set cannot separate the two models.
- At a 10:1 cost ratio, the threshold chosen on the training rows was 0.0905. On the test rows it cost 0.4692 per case, against 0.9437 at the default 0.5, roughly half.
- Across cost ratios from 2:1 to 50:1, the logistic model was cheaper at every ratio except 5:1. The gaps were small enough that I would not choose a model on them without a bootstrap interval at the ratio the business actually faces.
- At my chosen threshold, `roc_point()` gave a false positive rate of 0.296 and a true positive rate of 0.823.
