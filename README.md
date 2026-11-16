# NullStay

**An Explainable Hotel Booking Cancellation Prediction Dashboard**

NullStay is an AI Engineering project for hotel reservation staff and revenue management analysts. The planned dashboard will help staff explore historical booking patterns, estimate a booking’s probability of cancellation or no-show, and see which inputs influenced the prediction.

## Project Status

Weeks 1 and 2 are documented. The Week 3 data inspection and preparation pipeline ran in Google Colab, and two runs produced identical prepared CSV checksums. Model training, predictive evaluation, and dashboard implementation are planned.

## Problem Statement

Booking cancellations and no-shows create uncertainty about how many reservations will become actual stays. This complicates occupancy forecasting and operational planning. NullStay will investigate how historical booking data can support booking-level estimates and help staff interpret them.

## Evidence of the Problem

- António et al. (2017) investigate cancellation-related forecasting uncertainty using data from four resort hotels. [Predicting hotel booking cancellations to decrease uncertainty and increase revenue](https://doi.org/10.18089/tms.2017.13203)
- António et al. (2019) describe an automated cancellation prediction system that supports hotel operations. NullStay will evaluate its own predictions using its selected data and test design. [An Automated Machine Learning Based Decision Support System to Predict Hotel Booking Cancellations](https://doi.org/10.5334/dsj-2019-032)

## Existing Approaches

| Approach | Use and limitation |
|---|---|
| Historical cancellation rates | Provide a simple planning baseline. An overall rate assigns the same estimate to every booking. |
| Booking-level predictive models | Estimate outcomes from booking characteristics. Performance depends on the data and evaluation design. |
| Automated decision-support systems | Bring predictions into operational workflows. Measuring business impact requires evaluation in those workflows. |

## Engineering Gap

NullStay will combine input validation, booking-level probabilities, model explanations, and batch summaries in one dashboard. The project will compare its predictions with a historical-rate baseline and test whether staff can interpret the outputs.

## Project Objectives

- **O1:** Design a dashboard that helps staff explore historical booking and cancellation patterns.
- **O2:** Develop a prediction workflow that provides estimated probabilities and understandable explanations.
- **O3:** Evaluate predictive performance, probability reliability, usability, and processing speed.

## Planned Features and Boundaries

- Explore historical booking patterns by hotel type and arrival period.
- Estimate cancellation or no-show probability for a single booking.
- Validate and score compatible CSV uploads, then export results with row-level validation status.
- Show which inputs influenced a prediction and estimate batch cancellations or no-shows by summing the predicted probabilities for valid bookings.

Staff retain control of reservations, guest decisions, penalties, pricing, and overbooking. Model explanations describe how inputs influenced the estimate. The final feature set depends on which fields can be justified at the intended prediction time.

## Data Sources

- [Hotel Booking Demand — Kaggle](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)
- [Original dataset publication](https://doi.org/10.1016/j.dib.2018.11.126)
- [Supporting data dictionary](https://github.com/rfordatascience/tidytuesday/tree/main/data/2020/2020-02-11)

These references describe the same underlying dataset. The inspected CSV contains **119,390 bookings across 32 columns** from two hotels in Portugal, with arrival dates from **1 July 2015 to 31 August 2017**. The positive class in `is_canceled` includes **43,017 cancellations** and **1,207 no-shows**. See the [dataset notes](data/README.md) and [data inventory](docs/week3_data_inventory.md) for details.

## Weekly Progress and Deliverables

### Week 1 — Group Analysis and Project Scope

- [Group observation sheet](docs/week1_observation_sheet.md)
- [Group conceptual system map](docs/week1_system_map.png)
- [Individual NullStay project scope](docs/week1_project_scope.md)

The group observation sheet and system map document the group’s analysis of its assigned existing system. The individual scope defines NullStay.

### Week 2 — Requirements, Evaluation, and Architecture

- [Requirements specification](docs/week2_requirements.md) — functional, quality, and Responsible AI requirements.
- [Evaluation criteria](docs/week2_evaluation_criteria.md) — methods and acceptance targets for predictive performance, probability reliability, usability, latency, and other requirements.
- [V1 architecture diagram](docs/week2_architecture_v1.png) — model preparation and the dashboard workflow.
- [Architecture decisions](docs/week2_architecture_decisions.md) — design choices, trade-offs, and links from requirements to evaluation evidence.

### Week 3 — Tabular Data Engineering

- [Data inventory](docs/week3_data_inventory.md)
- [Colab pipeline notebook](notebooks/week3_pipeline.ipynb)
- [Pipeline diagram](docs/week3_pipeline_diagram.png)
- [Pipeline decisions and rerun evidence](docs/week3_pipeline_decisions.md)
- [Part B preparation manifest](data/week3_manifest.json)
- [Part C pipeline manifest](data/pipeline_v1/tabular/pipeline_manifest.json)

| Measure | Original | Prepared |
|---|---:|---:|
| Booking rows | 119,390 | 119,390 |
| Columns | 32 | 5 |
| Missing cells | 129,425 | 0 |
| Cancellation or no-show labels | 44,224 | 44,224 |

The pipeline preserved all booking rows and target values. The prepared table contains zero missing cells because the selected fields were complete. Both runs produced the same output SHA-256:

`7be5eaba12d514ccc33e041b0496ec1fb6b6740e451426346d7bc4be5cedef18`

## Reproduce the Week 3 Pipeline

1. Download `hotel_bookings.csv` from the dataset source.
2. Open the [Week 3 notebook](notebooks/week3_pipeline.ipynb) in Google Colab and run the cells in order. Upload the CSV when prompted.
3. Run **Cell 8B** to generate `prepared_table.csv` and `pipeline_manifest.json` under `/content/week3_project/data/pipeline_v1/tabular/`.
4. Run **Cell 9** to regenerate the table in a temporary directory and verify that both CSV checksums match.

## Repository Structure

| Folder | Purpose |
|---|---|
| `docs/` | Project scope, requirements, architecture, and lab evidence |
| `data/` | Dataset documentation and pipeline manifests |
| `notebooks/` | Data inspection, preparation, and pipeline notebooks |
| `src/` | Application source code |
| `evaluation/` | Evaluation materials and future results |
| `prompts/` | Reserved folder from the lab structure |