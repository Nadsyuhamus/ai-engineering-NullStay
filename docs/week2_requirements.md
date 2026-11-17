# Requirements Specification — NullStay

Version: 1.1 — Updated evidence and project objectives
Project: NullStay
Domain: Hospitality and Business Analytics

## 0. Project Context

### Problem

Hotel booking cancellations and no-shows create uncertainty when staff estimate occupancy and plan operations. An overall historical rate describes how often bookings ended in cancellation or no-show. NullStay will test whether booking characteristics can improve those estimates at the individual level.

### Supporting Evidence

1. A study of more than two million hotel bookings in Mallorca, Spain, from 2021 to 2024 found that cancellation likelihood varied with booking conditions such as refundability and lead time (Ramos Mir et al., 2026). [Full paper through Virginia Tech](https://vtechworks.lib.vt.edu/server/api/core/bitstreams/810d9c86-f74a-4687-b56e-6b88acefe914/content)
2. An automated cancellation prediction system was deployed in two hotels to investigate how forecasts could support operational demand management (Antonio et al., 2019). [Open-access journal article](https://reference-global.com/article/10.5334/dsj-2019-032)

### Existing Approaches and Engineering Gap

| Approach | Strength | Limitation relevant to NullStay |
|---|---|---|
| Historical cancellation rate | Provides a simple planning benchmark. See the forecasting context in the study of four resort hotels (Antonio et al., 2017). [Full paper on SciELO](https://scielo.pt/pdf/tms/v13n2/13n2a03.pdf) | One overall rate assigns the same estimate to every booking. |
| Explainable booking-level prediction | Combines prediction with an explanation of which inputs influenced the model. One study used a combination of models and SHAP explanations (Gómez-Talal et al., 2025). [Full author manuscript](https://burjcdigital.urjc.es/server/api/core/bitstreams/9e227b3a-9c5d-442e-9037-156922e762a2/content) | The study used city-hotel records. NullStay needs evidence for its city and resort subsets and will test whether users can interpret its explanations. |
| Automated cancellation decision support | Brings estimates into hotel workflows to support demand management (Antonio et al., 2019). [Open-access journal article](https://reference-global.com/article/10.5334/dsj-2019-032) | Measuring operational impact requires deployment and evaluation in the hotel workflow. |

NullStay’s planned contribution is a dashboard prototype that validates booking inputs and presents individual estimates, model explanations, and batch summaries. Because it adds probabilities to estimate batch outcomes, the evaluation will check the quality of those probabilities as well as predictive discrimination.

### Objectives

- **O1:** To design a dashboard that lets reservation staff view historical cancellation and no-show rates for their hotel by arrival period.
- **O2:** To develop a workflow that validates and scores single bookings and CSV batches, explains which inputs influenced individual estimates, and reports the expected number of cancellations or no-shows in a batch.
- **O3:** To evaluate predictive performance and probability quality against a historical-rate baseline using held-out records, and assess the dashboard against its usability and response-time targets.

### Intended Users

Hotel reservation staff and revenue-management analysts.

### Main User Tasks

- Explore historical booking outcomes by hotel type and arrival period.
- Estimate the probability of cancellation or no-show for a single booking.
- Upload compatible booking records for batch prediction and identify invalid rows.
- Review which inputs influenced individual estimates and view the estimated batch total.
- Export predictions and validation results for staff review.

### Data and Operating Context

NullStay uses the [Hotel Booking Demand dataset](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand), which contains historical records from two hotels in Portugal. The Week 3 inspection confirmed 119,390 records with arrival dates from 1 July 2015 to 31 August 2017.

The label `is_canceled = 1` includes cancellations and no-shows. In the tables below, **positive outcome** refers to either event. The intended prediction point is when a booking is entered or confirmed. The historical records provide limited evidence about which fields were available at that point, so input eligibility remains subject to review.

See the [data inventory](week3_data_inventory.md) for inspection results and the [dataset notes](../data/README.md) for candidate sources and access information.

### Constraints

- The project uses a limited historical dataset and student computing resources.
- Evaluation conclusions apply to the dataset and conditions tested. Use in current or Malaysian hotels would require further validation.
- Review whether each candidate input would be available at the intended prediction point.
- Fit learned preprocessing on training data and reserve the final test partition for evaluation.
- Follow the dataset source terms for access, storage, and redistribution.

### Out of Scope

- Automated cancellation, guest rejection, or guest penalties.
- Automated pricing and overbooking decisions.
- Revenue improvement guarantees.
- Live integration with hotel reservation systems.
- Causal claims about individual booking outcomes.

### Evaluation Focus

Evaluation will cover predictive discrimination, probability calibration, dashboard task completion, processing speed, data handling, and decision boundaries.

## 1. Functional Requirements

| ID | Requirement | Acceptance check |
|---|---|---|
| FR1 | The system shall display historical booking counts, positive-outcome counts, and positive-outcome rates, with filters for hotel type and arrival period. | Compare displayed values with independently calculated values for selected filters. |
| FR2 | The system shall accept one booking through a form, validate the supported inputs, and return an estimated probability of cancellation or no-show. | A valid booking produces a probability between 0 and 1. Invalid inputs produce a clear validation message. |
| FR3 | The system shall display up to three model-influential inputs alongside a prediction and explain that they describe model associations. | Inspect example predictions for consistent inputs, readable labels, and an explanation of their limits. |
| FR4 | The system shall accept a CSV with the required booking fields, produce a probability for each valid row, and identify invalid rows. | Test a valid file, a file missing required columns, and a file containing invalid rows. |
| FR5 | The system shall display batch booking counts and the sum of predicted probabilities as the expected number of cancellations or no-shows among valid bookings. | Compare the dashboard total with the sum of exported probabilities and confirm the total is labelled as an estimate. |
| FR6 | The system shall allow users to download batch results with a row identifier, prediction or validation status, and probability where available. | Inspect the downloaded results for correct row alignment, values, and validation messages. |

## 2. Non-Functional Requirements

These are proposed acceptance targets. Any revision after the data audit or baseline evaluation will be documented with its justification.

| ID | Quality attribute | Requirement and target | Measurement method |
|---|---|---|---|
| NFR1 | Predictive discrimination | On a held-out test set, the selected model shall achieve average precision at least 0.05 above the test-set positive-label prevalence. | Use average precision as the defined PR-AUC measure. Also report precision, recall, F1, and a confusion matrix at a threshold selected using validation data. |
| NFR2 | Probability quality | The selected model shall achieve a lower Brier score than a constant-probability baseline using the training-set positive-label rate. | Compare both on the same held-out test records and inspect a calibration plot. |
| NFR3 | Response time | After application and model loading, the system shall return a single prediction within 2 seconds at the 90th percentile and process a valid 1,000-row CSV within 10 seconds at the 90th percentile. | Time 30 single predictions and 10 batch runs. Record the device and test conditions. |
| NFR4 | Usability and clarity | At least 4 of 5 formative test participants shall complete the single-booking and batch-export tasks without assistance and correctly interpret a probability as uncertain. | Observe task completion, ask a short interpretation question, and record participants’ relevant experience. |
| NFR5 | Numerical correctness | Dashboard aggregates and exported predictions shall match reference calculations across at least five selected test cases. | Compare counts exactly, rates within 0.01 percentage points, and exported probabilities within 0.000001 of model outputs. |
| NFR6 | Reproducibility | Rerunning the documented training workflow with the same data, configuration, seed, and environment shall reproduce the same test split and evaluation metrics within 0.0001. | Compare split identifiers and metrics from two runs. |

The usability study is a small formative evaluation that will guide improvements to the prototype.

## 3. Responsible AI Requirements

| ID | Risk | Requirement | Evaluation method and acceptance target |
|---|---|---|---|
| RAI1 | Overreliance on predictions | The system shall present predictions as uncertain estimates and shall not automatically cancel, reject, or penalise bookings. | Inspect all prediction views for uncertainty wording and confirm that no automated booking-action function exists. |
| RAI2 | Leakage and misleading performance | The training workflow shall exclude the target and known post-outcome fields from model inputs. It shall document the eligibility of every retained feature. | Audit all retained features. Exclude `reservation_status`, `reservation_status_date`, and fields whose availability at the intended prediction point cannot be justified. Fit learned preprocessing only on training data. |
| RAI3 | Misleading explanations | The system shall describe explanations as model influences and avoid causal or certain claims about an individual booking. | Inspect at least 10 example explanations. All must describe their limitation and avoid causal or certain wording. |
| RAI4 | Uneven performance across hotel contexts | Evaluation shall report performance separately for City Hotel and Resort Hotel when each test subset contains at least 100 records and both outcomes. | Report subgroup sample sizes, precision, recall, PR-AUC, and Brier score. A recall difference above 0.15 triggers investigation. |
| RAI5 | Exposure of uploaded booking data | The prototype shall process CSV uploads without intentionally retaining their contents and shall exclude guest-identifying fields from the required input schema. | Inspect storage and logs after a test upload. Confirm that no application-created upload file remains and that names, emails, and phone numbers are outside the required fields. |

The subgroup checks assess performance across the two available hotel contexts.