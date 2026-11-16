# NullStay Pipeline Decisions — Lab 3 Part C

[Pipeline diagram](week3_pipeline_diagram.png) · [Colab notebook](../notebooks/week3_pipeline.ipynb)

## Pipeline stages

| Stage | Input | Output or failure response |
|---|---|---|
| Ingest | Raw `hotel_bookings.csv` | Loaded booking records and a source SHA-256 checksum. |
| Inspect | Loaded records | Continue when the file contains records and all required columns. Stop and report missing columns or an empty file. |
| Prepare | Inspected records | Derive `arrival_date`. Retain `hotel`, `lead_time`, and `arrival_date_month` as candidate model inputs and `is_canceled` as the target. |
| Validate | Prepared table | Check retained values, hotel groups, binary labels, and preservation of rows and targets. Stop before writing if a check fails. |
| Store and record lineage | Validated table | Write `prepared_table.csv` and `pipeline_manifest.json`. The manifest records the source and output checksums, dimensions, retained fields, and target definition. |

## Preparation choice

The pipeline uses the five-column configuration tested in Part B. Preparation reduced the table from 32 columns to 5 and preserved all 119,390 bookings and all 44,224 positive labels. Missing cells went from 129,425 to zero because the selected fields were complete. No values were filled and no records were removed.

The full arrival date supports historical filtering and split planning. Candidate model inputs still require a booking-time eligibility review. The target includes both cancellations and no-shows. Final reservation status fields are excluded from the prepared table because they reveal outcome information.

## Processing choice

NullStay uses batch extract, transform, and load (ETL). The notebook reads one historical CSV, transforms and validates it, then saves a prepared file. Batch processing suits the current dataset because it is a fixed snapshot. A streaming design would require a live source and a way to handle ongoing updates. An ELT design would require a destination where the raw data is loaded and transformed.

## Rerun evidence

Cell 8B produced 119,390 rows, 5 columns, and 44,224 positive labels. Cell 9 reran the pipeline from the same source file in a temporary directory and confirmed that both source checksums and both output checksums matched.

Both runs produced the following output SHA-256:

`7be5eaba12d514ccc33e041b0496ec1fb6b6740e451426346d7bc4be5cedef18`

The check establishes repeatable CSV output for this source and notebook environment. Booking-time feature availability, model performance, and reproducibility across environments remain to be evaluated.