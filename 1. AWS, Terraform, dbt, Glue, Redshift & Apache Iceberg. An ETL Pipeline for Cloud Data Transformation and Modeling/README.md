# An ETL Pipeline for Cloud Data Transformation and Modeling using AWS, Terraform, dbt, Glue, Redshift & Apache Iceberg

## Overview

This project demonstrates an end-to-end cloud data engineering pipeline for **DeFtunes**, a music platform expanding into digital song purchases. The pipeline integrates song metadata from a PostgreSQL operational database with purchase-session and user data from REST APIs, then transforms and models the data for analytics.

The project is adapted from the **Data Engineering Capstone Project by [DeepLearning.AI](https://www.deeplearning.ai/)**. The AWS resources and datasets were provided by the course strictly for educational purposes.

## Architecture

The solution follows a medallion-style data architecture:

```text
PostgreSQL RDS ──┐
                 ├── AWS Glue ETL ──> S3 Landing Zone
Purchase APIs ───┘                         │
                                           ▼
                                  AWS Glue Transformations
                                           │
                                           ▼
                              S3 Transformation Zone
                               Apache Iceberg Tables
                                           │
                                           ▼
                              Redshift Spectrum / Glue Catalog
                                           │
                                           ▼
                              dbt Serving Layer and Star Schema
```

## Technology Stack

| Area | Technologies |
|---|---|
| Infrastructure as code | Terraform |
| Data ingestion and transformation | AWS Glue ETL, Python |
| Data lake storage | Amazon S3, Apache Iceberg |
| Data warehouse | Amazon Redshift, Redshift Spectrum |
| Data transformation and modeling | dbt, SQL |
| Source systems | PostgreSQL RDS, REST APIs |
| Data exploration | Jupyter Notebook, pandas, SQL |

## Pipeline Workflow

1. **Extract:** AWS Glue jobs ingest song data from PostgreSQL and session/user data from REST API endpoints into the S3 landing layer.
2. **Transform:** Glue jobs standardize fields, normalize nested API structures, add ingestion metadata, and write queryable Apache Iceberg tables to the transformation layer.
3. **Expose:** Redshift Spectrum connects Redshift to the Glue Catalog and enables queries against Iceberg tables stored in S3.
4. **Model:** dbt builds the analytical serving layer in Redshift using fact and dimension models organized as a star schema.
5. **Validate:** SQL queries, pandas profiling, Redshift schema checks, and dbt connection validation confirm that the data is accessible and ready for analysis.

## Key Project Features

- Multi-source ingestion from a relational database and REST APIs.
- Reproducible AWS infrastructure provisioned with Terraform.
- Separate landing, transformation, and serving layers.
- Apache Iceberg tables for the transformed data lake layer.
- Redshift Spectrum integration with the AWS Glue Catalog.
- dbt-based dimensional modeling for analytical workloads.
- Exploratory data analysis using pandas and SQL.

## Repository Contents

- `Cloud_Data_Transformation_&_Modeling.ipynb` — project walkthrough, data exploration, pipeline execution, and validation.
- `terraform/` — infrastructure definitions and Glue job modules.
- `terraform/assets/` — extraction and transformation scripts.
- `dbt_modeling/` — dbt project and serving-layer models.
- `images/` — architecture and project documentation assets.

## Security Note

The notebook was executed in a course-provided AWS environment. Before publishing this project, remove or replace any credentials, private endpoints, temporary bucket names, and other environment-specific values. Use environment variables or a secrets manager for production deployments, and never commit passwords or access keys to version control.

## Future Improvements

Potential next steps include adding workflow orchestration, automated data-quality tests, incremental processing, monitoring and alerting, and business-facing dashboards.

## License and Attribution

This project is intended for educational and portfolio use. It is based on the Data Engineering Capstone Project provided by DeepLearning.AI, with course-provided AWS resources and datasets.
