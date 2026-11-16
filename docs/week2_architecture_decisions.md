# NullStay Architecture Decisions — Lab 2 Part C

[The architecture diagram](week2_architecture_v1.png) shows two workflows. During development, the booking dataset is checked, models are evaluated, and the selected model is saved with its preprocessing steps. In the dashboard, staff can view historical cancellation trends or submit bookings for prediction.

## Component responsibilities

| Component                      | Responsibility                                                                                                                                                                                                                        |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Data preparation               | Audit feature availability and leakage, prepare historical summaries, and establish the data partitions before fitting preprocessing.                                                                                                 |
| Model development              | Compare the historical-rate baseline, logistic regression, and a tree-based candidate. Select the model using validation results and evaluate it on held-out data.                                                                    |
| Dashboard and input validation | Display historical trends, accept a single booking or CSV, and show which inputs or rows need correction.                                                                                                                             |
| Prediction and explanation     | Apply the saved preprocessing and model to valid records. Return cancellation probabilities and identify inputs that influenced the model.                                                                                            |
| Results and export             | Show probabilities as uncertain estimates, sum valid probabilities for the estimated batch cancellation count, and export results with row-level validation status. Staff review the results and retain control of booking decisions. |

## Decision 1 — Save the model and preprocessing together

**Alternatives considered:** Fit the model when the application starts, or train it separately and load the saved model and preprocessing in the dashboard.

**Choice:** Train separately and save the model with the preprocessing fitted on the training data. The dashboard uses these saved components whenever it scores a booking.

**Requirements and trade-off:** This supports response time (NFR3), reproducibility (NFR6), and leakage prevention (RAI2). It requires a deliberate retraining and release step whenever the model or its inputs change.

## Decision 2 — Validate bookings before prediction

**Alternatives considered:** Rely on the model to raise errors for unsupported inputs, or check the inputs against the booking schema before scoring.

**Choice:** Validate the single-booking form before scoring. For CSV uploads, check the required columns first, then score valid rows and report errors for invalid rows.

**Requirements and trade-off:** This supports FR2, FR4, and FR6 by giving staff specific correction messages and keeping each result aligned with its input row. The validation rules must be kept in sync with the model’s expected inputs.

## Requirements traceability

| Objective                                 | Requirement   | Evaluation criterion | Architecture component                   | Evidence to collect                                                                                      |
| ----------------------------------------- | ------------- | -------------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| O1 — Explore historical patterns          | FR1, NFR5     | EC5                  | Historical summaries and dashboard       | Independently calculated counts and rates for selected filters                                           |
| O2 — Provide predictions and explanations | FR2, FR4, FR6 | EC5                  | Input validation, prediction, and export | Tests with a valid booking, a CSV missing a required column, and a CSV with mixed valid and invalid rows |
| O2 — Provide predictions and explanations | FR3, RAI3     | EC3                  | Prediction explanation and results       | Review of 10 example explanations for readability and wording about model influence                      |
| O2 — Provide predictions and explanations | RAI1, RAI5    | EC8                  | Results and CSV handling                 | Inspection of uncertainty wording, booking actions, and upload storage and logs                          |
| O3 — Evaluate the prototype               | NFR2          | EC2                  | Model development and evaluation         | Held-out Brier scores for the selected model and the historical-rate baseline                            |
| O3 — Evaluate the prototype               | NFR6, RAI2    | EC6                  | Data preparation and model development   | Feature eligibility audit and a comparison of split identifiers and metrics across two runs              |

## Open decision

The data audit will determine which fields can be justified at the intended prediction point. If the historical records cannot establish booking-time availability, the dashboard will describe its scores as retrospective estimates.

The architecture and acceptance checks are planned. Evaluation results will be recorded after implementation.
