# 📥 Data Guide

This folder is intentionally empty of raw data. The original ORR CSV files are **not** stored in the repository.

## Source

**UK Office of Rail and Road (ORR)** — freight statistics, available from the ORR data portal (https://dataportal.orr.gov.uk). Please check the portal's licence and attribution terms before reusing the data.

## Tables used

| Table | Title | Use in the notebook |
|---|---|---|
| 1314 | Freight moved by commodity (periodic) | Builds the aggregate freight time series used for forecasting |
| 1311 | Freight moved by Network Rail region (periodic) | Inspected during exploratory data analysis |

**Forecasting target column:** `Total (million net tonne kilometers)`

## Expected filenames

The notebook currently reads these files:

- `Table 1314_ Freight moved by commodity (periodic).csv`
- `Table 1311_ Freight moved by Network Rail region (periodic).csv`

## How to use

1. Download both tables from the ORR data portal as CSV.
2. Rename them to match the filenames above (or edit the paths in the notebook).
3. Place them in this `data/` folder, or upload them to your Google Colab session.
4. Run the notebook from top to bottom.

## Notes

- CSV files in this folder are ignored by Git (see `.gitignore`).
- Do **not** commit restricted, confidential or personally sensitive data.
