# Week 1 Project Scope — NullStay

## Project Name

NullStay: An Explainable Hotel Booking Cancellation Prediction Dashboard

## Domain

Hospitality / Business Analytics / Machine Learning

## Problem Statement

Hotel booking cancellations and no-shows create uncertainty about how many reservations will become actual stays. This complicates occupancy forecasting and operational planning. An overall historical rate gives staff a starting point, but it assigns the same estimate to bookings with different characteristics.

NullStay proposes a dashboard that estimates the probability of cancellation or no-show from selected booking information. It will show which inputs influenced each displayed prediction and summarise the expected number of cancellations or no-shows across a batch of bookings.

The prototype will be developed and evaluated using historical records. Hotel staff will retain control of booking decisions.

## Evidence of the Problem

### Evidence 1 — Cancellations affect demand forecasting

A study that deployed an automated cancellation prediction system in two hotels examined how cancellation estimates could support demand forecasting and hotel operations (Antonio et al., 2019).

This establishes the practical context for NullStay. The project will evaluate its own predictions and interface using its selected dataset and test conditions.

**Reference**
Antonio, N., de Almeida, A., & Nunes, L. (2019). An automated machine learning based decision support system to predict hotel booking cancellations. *Data Science Journal, 18*(1), Article 32.

[Read the open-access journal article](https://reference-global.com/article/10.5334/dsj-2019-032)

### Evidence 2 — Cancellation likelihood varies across booking conditions

An analysis of more than two million hotel bookings in Mallorca, Spain, from 2021 to 2024 found that cancellation likelihood varied with conditions such as refundability and how far in advance the reservation was made (Ramos Mir et al., 2026).

This motivates testing whether booking-level probability estimates improve on an overall historical rate for the NullStay dataset.

**Reference**
Ramos Mir, V., Deyá Tortella, B., Leoni, V., & Nicolau, J. L. (2026). Decoding booking cancellations: Quantitative insights and theoretical advances in tourism behavior. *Tourism Management, 114*, Article 105384.

[Read the full paper through Virginia Tech](https://vtechworks.lib.vt.edu/server/api/core/bitstreams/810d9c86-f74a-4687-b56e-6b88acefe914/content)

## Initial Related Work / Existing Approaches

### 1. Historical cancellation-rate forecasting

**Approach**
Estimate future cancellations using an overall historical rate or separate rates for selected booking groups.

**Key limitation**
An overall average assigns the same estimate to every booking. Rates for small groups may also be unstable.

**Relevance to NullStay**
Use the positive-label rate from the training data as a simple baseline for evaluating booking-level probabilities.

**Source for the broader forecasting context**
A study of four resort hotels examined how booking-level cancellation forecasts could improve estimates of hotel demand (Antonio et al., 2017). [Read the full paper on SciELO](https://scielo.pt/pdf/tms/v13n2/13n2a03.pdf)

### 2. Booking-level machine-learning prediction

**Study**
An explainable prediction study combined several machine learning models and used SHAP to show which booking features influenced the model’s predictions (Gómez-Talal et al., 2025).

**Key limitation**
The study used city-hotel records. Evaluation across NullStay’s city and resort subsets requires evidence from both groups.

**Relevance to NullStay**
Compare manageable models on held-out data and test whether users can interpret the explanations shown beside the estimates.

**Source**
Gómez-Talal, I., Azizsoltani, M., Talón-Ballestero, P., & Singh, A. (2025). Machine learning in hospitality: Interpretable forecasting of booking cancellations. *IEEE Access, 13*, 26622–26638. [Read the full author manuscript](https://burjcdigital.urjc.es/server/api/core/bitstreams/9e227b3a-9c5d-442e-9037-156922e762a2/content)

### 3. Automated cancellation decision-support systems

**Study**
An automated decision-support system generated cancellation predictions for use in two hotels (Antonio et al., 2019).

**Key limitation**
Its operational results depend on how the system was deployed and how staff acted on its predictions. Establishing business impact for NullStay would require a separate operational evaluation.

**Relevance to NullStay**
Build a dashboard that supports staff review and evaluate its predictions, explanations, and processing speed under documented test conditions.

**Source**
[Read the open-access journal article](https://reference-global.com/article/10.5334/dsj-2019-032)

## Engineering Gap / Limitation

NullStay’s engineering challenge is to turn historical booking records into estimates staff can interpret. The dashboard must validate booking inputs, use fields that can be justified at the intended prediction point, and explain how the model used those inputs.

The dashboard will sum individual probabilities to estimate the number of cancellations or no-shows in a batch. This makes probability quality important. A model can rank bookings by risk while consistently overstating the expected number of cancellations or no-shows.

## Initial Project Objectives

- **O1:** To design a dashboard that lets reservation staff view historical cancellation and no-show rates for their hotel by arrival period.
- **O2:** To develop a workflow that validates and scores single bookings and CSV batches, explains which inputs influenced individual estimates, and reports the expected number of cancellations or no-shows in a batch.
- **O3:** To evaluate predictive performance and probability quality against a historical-rate baseline using held-out records, and assess the dashboard against its usability and response-time targets.

## Target Users

Hotel reservation staff and revenue-management analysts.

These are the intended users. User testing will assess whether participants can complete the planned tasks and interpret the displayed probabilities.

## Initial Supported Tasks

- Explore historical booking outcomes by hotel type and arrival period.
- Estimate the probability of cancellation or no-show for a compatible booking record.
- Upload a compatible CSV and identify rows that need correction.
- Review which inputs influenced an individual estimate.
- View the expected number of cancellations or no-shows in a batch.
- Export predictions and validation results for staff review.

Model explanations describe how the fitted model used its inputs.

## Initial Prediction Task

Predict the outcome represented by `is_canceled` using an audited subset of booking fields. The Week 3 inspection confirmed that label `1` includes both cancellations and no-shows. NullStay will display the estimate as **“Probability of cancellation or no-show.”**

The intended prediction point is when a reservation is entered or confirmed. The historical records provide limited evidence about which fields were available at that point. The final feature set remains subject to review. Final reservation status and status date are excluded from model inputs because they reveal outcome information.

## Data Sources

### Primary Application Dataset

NullStay uses the Hotel Booking Demand dataset, accessed through Kaggle. The Week 3 inspection confirmed **119,390 bookings** across **32 columns**, comprising **40,060 Resort Hotel** records and **79,330 City Hotel** records. Arrival dates range from **1 July 2015 to 31 August 2017**.

**Kaggle**
[Download the Hotel Booking Demand dataset](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)

**Original dataset publication**
Antonio, N., de Almeida, A., & Nunes, L. (2019). Hotel booking demand datasets. *Data in Brief, 22*, 41–49. [Read the dataset publication](https://doi.org/10.1016/j.dib.2018.11.126)

### Supporting Data Dictionary

The field definitions support schema interpretation and the review of candidate model inputs. [Open the Hotel Booking Demand data dictionary](https://github.com/rfordatascience/tidytuesday/tree/main/data/2020/2020-02-11)

### Data-Source Clarification

The Kaggle CSV, dataset publication, and data dictionary document one underlying application dataset. NullStay follows Topic 3’s tabular pathway and inspected two subsets of that dataset: City Hotel and Resort Hotel. The research papers above support the problem statement and comparison with existing approaches.

The [Week 3 data inventory](week3_data_inventory.md) records the inspection findings. The [pipeline manifest](../data/pipeline_v1/tabular/pipeline_manifest.json) records the source and output checksums.

## Similar to EduAdvisor

Both applications require clear requirements, traceable data, data preparation, evaluation, and Responsible AI controls. Both must communicate the limitations of their outputs and be evaluated against defined acceptance criteria.

## Different from EduAdvisor

EduAdvisor uses retrieved documents to answer questions. NullStay uses structured hotel booking records to estimate probabilities and display results through a dashboard. NullStay’s evaluation focuses on predictive performance, probability quality, usability, and processing speed.

## Responsible AI Risks

### Risk 1 — Incorrect predictions and overreliance

**Potential harm**
Staff may treat an uncertain estimate as a definite outcome and make unsuitable planning or guest decisions.

**Proposed control**
Label probabilities as estimates and keep staff responsible for reservations, guest penalties, pricing, and overbooking.

### Risk 2 — Information leakage

**Potential harm**
Using final reservation status or other later information could produce misleadingly strong evaluation results.

**Proposed control**
Audit feature availability, document excluded inputs, fit learned preprocessing on training data, and keep the final test data separate from model development.

### Risk 3 — Unequal model performance

**Potential harm**
Prediction errors may differ between city and resort bookings.

**Proposed control**
Report sample sizes and evaluate each hotel subset when the test data support meaningful comparisons.

### Risk 4 — Privacy in uploaded files

**Potential harm**
Uploaded booking files may contain names, contact details, or other unnecessary personal information.

**Proposed control**
Define the accepted input schema, keep guest identifiers outside the required inputs, and process uploaded files without intentionally retaining them.

### Risk 5 — Historical-data limitations

**Potential harm**
Staff may assume that performance on historical records from two Portuguese hotels applies to current hotels in other locations.

**Proposed control**
State the dataset’s location and date range, describe the evaluation conditions, and require relevant validation before operational use.