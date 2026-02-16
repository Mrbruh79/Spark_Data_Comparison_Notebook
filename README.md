# Spark_Data_Comparison_Notebook

`data.ipynb` provides a Spark-based reconciliation workflow for comparing two datasets (catalog tables or S3 parquet paths) and exporting comparison reports.

## Features

- Row-count comparison between source systems
- Column-level null and distinct count profiling
- Missing-record detection using a primary key
- Optional date-filtered and date-wise analysis
- CSV/Excel export of analysis artifacts
- Batch execution across multiple dataset pairs

## Main File

- Notebook: `data.ipynb`

## Requirements

- Python 3.9+
- `pyspark`
- `pandas`
- `boto3`
- `openpyxl` (for Excel export)

Install:

```bash
pip install pyspark pandas boto3 openpyxl
```

## Quick Start

1. Open `data.ipynb` in VS Code/Jupyter.
2. Run cells top to bottom.
3. Use either:
   - The real-source template (commented example), or
   - The built-in local smoke test with temp views.

## Important Behavior

- `run_full_analysis()` only computes and stores results in memory.
- Files are created only when export methods are called:
  - `analyzer.export_to_csv()`
  - `analyzer.export_to_excel()`
  - `analyzer.export_missing_records()`

## Core Configuration (`DataComparisonConfig`)

Required fields:

- `source_1_table`
- `source_2_table`
- `primary_key`
- `domain_name`

Common optional fields:

- `count_key`
- `datetime_columns`
- `filter_date_start`, `filter_date_end` (`YYYYMMDD`)
- `partition_column`
- `output_base_path`
- `enable_persist`
- `aws_region`, `aws_access_key_id`, `aws_secret_access_key`, `aws_session_token`

## Example Output Files

For the smoke test (`domain_name="smoke_test"`), expected outputs are:

- `outputs/smoke_test_comparison.csv`
- `outputs/smoke_test_comparison.xlsx`
- `outputs/smoke_test_missing_in_source_1.xlsx`
- `outputs/smoke_test_missing_in_source_2.xlsx`

## Environment Notes

- Local Spark: use local paths like `./outputs`.
- Databricks: use `/dbfs/FileStore/<folder>` or `dbfs:/FileStore/<folder>`.
- Databricks Connect is supported if configured.

## Batch Processing

Use `run_batch_comparison(...)` to run multiple `DataComparisonConfig` entries in one pass. The batch flow logs failures per dataset and continues processing remaining entries.

## Push Checklist

Before pushing, include only project files (for example `data.ipynb`, `README.md`, and optionally `.gitignore`) and exclude generated outputs if you do not want large artifacts in git history.
