# NullStay

**An Explainable Hotel Booking Cancellation Prediction Dashboard**

## Overview

NullStay is a proposed dashboard for hotel reservation staff and revenue-management analysts. It will help staff explore historical booking outcomes, estimate the probability of cancellation or no-show for individual bookings, and review the expected number of cancellations or no-shows across a batch.

## Project Status

Weeks 1 and 2 documented the project scope, requirements, evaluation criteria, and architecture. In Week 3, the dataset was inspected and a repeatable preparation pipeline was run in Google Colab. Both pipeline runs produced identical prepared CSV checksums. Model training, predictive evaluation, and dashboard implementation are planned.

## Problem Statement

Cancellations and no-shows create uncertainty about how many reservations will become actual stays. This complicates occupancy forecasting and operational planning. An overall historical rate provides a starting point. NullStay will test whether booking-level probabilities provide a better estimate for its dataset.

## Evidence of the Problem

- Cancellation likelihood varied with booking conditions such as refundability and lead time in a study of more than two million hotel bookings in Mallorca, Spain, from 2021 to 2024 (Ramos Mir et al., 2026). [Full paper through Virginia Tech](https://vtechworks.lib.vt.edu/server/api/core/bitstreams/810d9c86-f74a-4687-b56e-6b88acefe914/content)
- An automated cancellation prediction system was deployed in two hotels to investigate how predictions could support demand management (Antonio et al., 2019). [Open-access journal article](https://reference-global.com/article/10.5334/dsj-2019-032)

## Existing Approaches

| Approach | Use and limitation |
|---|---|
| Historical cancellation rate | Provides a simple baseline. An overall rate assigns the same estimate to every booking. The Week 1 scope links the forecasting context (Antonio et al., 2017). [Full paper on SciELO](https://scielo.pt/pdf/tms/v13n2/13n2a03.pdf) |
| Explainable booking-level prediction | A study combined multiple models and used SHAP to show which inputs influenced predictions. Its evaluation used city-hotel records (Gómez-Talal et al., 2025). [Full author manuscript](https://burjcdigital.urjc.es/server/api/core/bitstreams/9e227b3a-9c5d-442e-9037-156922e762a2/content) |
| Automated hotel decision support | Integrates predictions into hotel operations. Measuring business impact requires evaluation in the operational workflow (Antonio et al., 2019). [Open-access journal article](https://reference-global.com/article/10.5334/dsj-2019-032) |

## Engineering Gap

NullStay will combine input validation, booking-level probabilities, model explanations, and batch summaries in one dashboard. The batch estimate will be the sum of predicted probabilities for valid bookings. Evaluation will therefore check probability quality as well as how well the model ranks bookings by risk.

## Project Objectives

- **O1:** To design a historical dashboard that lets reservation staff compare the proportion of bookings that ended in cancellation or no-show by hotel type and arrival period.
- **O2:** To develop a workflow that validates and scores single bookings and CSV batches, explains which inputs influenced individual estimates, and reports the expected number of cancellations or no-shows in a batch.
- **O3:** To evaluate predictive performance and probability quality against a historical-rate baseline using held-out records, and assess the dashboard against its usability and response-time targets.

## Target Users

Hotel reservation staff and revenue-management analysts.

## Planned Features

- Explore historical booking outcomes by hotel type and arrival period.
- Estimate the probability of cancellation or no-show for a single booking.
- Validate and score compatible CSV files, with feedback for invalid rows.
- See which inputs influenced an individual estimate and view the expected batch total.
- Export batch predictions and validation results for staff review.

## Boundaries

Staff retain control over reservations, guest penalties, pricing, and overbooking. Model explanations describe how inputs influenced the model’s estimate. The prototype uses historical data from two Portuguese hotels, and the final feature set depends on which inputs can be justified at the intended prediction point.

## Data Sources

- [Hotel Booking Demand CSV on Kaggle](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)
- [Original dataset publication](https://doi.org/10.1016/j.dib.2018.11.126)
- [Supporting data dictionary](https://github.com/rfordatascience/tidytuesday/tree/main/data/2020/2020-02-11)

These sources document one underlying dataset. The inspected CSV contains **119,390 bookings across 32 columns**, with arrival dates from **1 July 2015 to 31 August 2017**. The positive class in `is_canceled` includes **43,017 cancellations** and **1,207 no-shows**. See the [dataset notes](data/README.md) and [data inventory](docs/week3_data_inventory.md) for details.

## Weekly Progress and Deliverables

### Week 1 — Group Analysis and Project Scope

- [Group observation sheet](docs/week1_observation_sheet.md)
- [Group conceptual system map](docs/week1_system_map.png)
- [Individual NullStay project scope](docs/week1_project_scope.md)

The observation sheet and system map document the group’s analysis of its assigned existing system. The individual scope defines NullStay.

### Week 2 — Requirements, Evaluation, and Architecture

- [Requirements specification](docs/week2_requirements.md)
- [Evaluation criteria](docs/week2_evaluation_criteria.md)
- [V1 architecture diagram](docs/week2_architecture_v1.png)
- [Architecture decisions and requirements traceability](docs/week2_architecture_decisions.md)

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

The pipeline preserved all booking rows and target values. The prepared table has zero missing cells because the retained fields were complete. Both runs produced the same output SHA-256 checksum.

`7be5eaba12d514ccc33e041b0496ec1fb6b6740e451426346d7bc4be5cedef18`

## Reproduce the Week 3 Pipeline

1. Download `hotel_bookings.csv` from the dataset source.
2. Open the [Week 3 notebook](notebooks/week3_pipeline.ipynb) in Google Colab and run the cells in order. Upload the CSV when prompted.
3. Run **Cell 8B** to generate `prepared_table.csv` and `pipeline_manifest.json` in `/content/week3_project/data/pipeline_v1/tabular/`.
4. Run **Cell 9** to regenerate the table in a temporary directory and compare the two CSV checksums.

## Repository Structure

| Folder | Purpose |
|---|---|
| `docs/` | Project scope, requirements, architecture, and lab evidence |
| `data/` | Dataset documentation and pipeline manifests |
| `notebooks/` | Data inspection, preparation, and pipeline notebooks |
| `src/` | Application source code |
| `evaluation/` | Evaluation materials and future results |
| `prompts/` | Reserved folder from the lab structure |