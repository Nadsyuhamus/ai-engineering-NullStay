# NullStay Data Inventory — Lab 3 Part A

## Source and ingestion

- **Dataset:** Hotel Booking Demand
- **Source:** [Kaggle download](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)
- **Original publication:** [Hotel Booking Demand Datasets](https://doi.org/10.1016/j.dib.2018.11.126)
- **File inspected:** `hotel_bookings.csv` (16,855,599 bytes)
- **Format and size:** CSV, 119,390 rows × 32 columns
- **Inspection evidence:** `notebooks/week3_pipeline.ipynb`. The CSV was loaded in Google Colab with pandas. The initial inspection kept all rows as loaded.
- **Access and redistribution:** The file was downloaded from Kaggle and uploaded to Colab for inspection. Record the source’s licence and confirm redistribution terms before sharing dataset files.

## Subsets inspected

The notebook inspected City Hotel and Resort Hotel as two subsets of the same dataset. It previewed two rows from each subset, including arrival year and month, lead time, and the target.

| Subset | Target `0` | Target `1` | Total rows | Share labelled `1` |
|---|---:|---:|---:|---:|
| City Hotel | 46,228 | 33,102 | 79,330 | 41.73% |
| Resort Hotel | 28,938 | 11,122 | 40,060 | 27.76% |
| Overall | 75,166 | 44,224 | 119,390 | 37.04% |

`is_canceled` is the prediction target. All 75,166 records labelled `0` have the final status `Check-Out`. The 44,224 records labelled `1` include 43,017 `Canceled` records and 1,207 `No-Show` records. NullStay will use the existing binary target and label the prediction “Probability of cancellation or no-show.”

The observed arrival dates range from 1 July 2015 to 31 August 2017. All 119,390 arrival dates parsed successfully.

## Missing values

| Column | Missing rows | Initial decision |
|---|---:|---|
| `company` | 112,593 | Hold out of initial model inputs. Check whether a missing value means no company was associated with the booking. |
| `agent` | 16,340 | Check the meaning of missing values and whether this field is available at prediction time. |
| `country` | 488 | Exclude from the initial model feature set. |
| `children` | 4 | Inspect the affected records and document a handling decision during preparation. |

The initial inspection left missing values as loaded. Any preprocessing parameters learned from the data will be fitted on the training partition only.

## Requirements-to-data mapping

| Requirement | Data needed | Check or limitation |
|---|---|---|
| FR1 — Historical trends | `hotel`, arrival date fields, and `is_canceled` | The notebook confirmed both hotel groups and both target values. Validate the arrival date range before building period filters. |
| RAI2 — Leakage prevention | `is_canceled` for labels and an audited list of candidate inputs | Keep the target out of model inputs. Exclude `reservation_status` and `reservation_status_date` from model inputs because they describe the eventual outcome. |
| RAI4 — Hotel context evaluation | `hotel`, actual outcomes, and future model probabilities | Both hotel groups contain both outcomes in the full file. Check subgroup sample sizes again after the test split. |

## Limits and next checks

The records describe historical bookings from two Portuguese hotels. Complete booking-time snapshots are unavailable, so feature eligibility remains provisional. Before preparation, verify the observed arrival date range, how no-shows are labelled, and the meaning of missing values in candidate features.