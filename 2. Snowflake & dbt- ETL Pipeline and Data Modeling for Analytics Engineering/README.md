# Snowflake & dbt: ETL Pipeline and Data Modeling for Analytics Engineering

This project demonstrates a modern analytics workflow in which **Snowflake** provides the cloud data warehouse and **dbt** performs the transformation, testing, and documentation layer. The pipeline uses Snowflake’s sample **TPC-H** data, transforms it locally with dbt, and materializes analytical views and tables back in Snowflake.

## Project workflow

The workflow follows a simple ELT pattern:

1. Snowflake stores the source TPC-H `ORDERS` and `LINEITEM` data.
2. dbt connects to Snowflake from a local development environment.
3. Staging models standardize source columns and create a surrogate key for each order item.
4. Intermediate models join orders to line items and calculate order-item discounts and sales summaries.
5. The final mart model produces an order-level fact table in Snowflake.
6. dbt tests validate keys, relationships, accepted order statuses, dates, and discount calculations.

```text
Snowflake TPC-H sample data
        |
        v
  dbt staging views
        |
        v
 dbt intermediate models
        |
        v
  fct_orders table
```

## Technologies

| Technology | Purpose |
|---|---|
| Snowflake | Cloud data warehouse and source-data platform |
| dbt | SQL transformations, model dependency management, and testing |
| dbt-utils | Surrogate-key generation through `generate_surrogate_key` |
| SQL/Jinja | Transformations and reusable pricing logic |

## Repository structure

| Path | Description |
|---|---|
| `models/staging/` | Staging views for Snowflake TPC-H orders and line items |
| `models/marts/` | Intermediate models and the final `fct_orders` table |
| `macros/pricing.sql` | Reusable macro for calculating discounted amounts |
| `tests/` | Singular SQL tests for valid order dates and discount values |
| `models/*/*.yml` | Source declarations, column tests, and model tests |
| `dbt_project.yml` | Project settings and Snowflake materialization configuration |
| `packages.yml` | dbt package dependency configuration |

## Models and data quality

<img width="1355" height="637" alt="snowflake sc" src="https://github.com/user-attachments/assets/88c8e03d-f59d-4344-a288-dcf4d042cbe6" />
Figure: Snowflake worksheet used to provision the dbt environment, including the warehouse, database, role, schema, and required access grants.

The staging layer exposes `stg_tpch_orders` and `stg_tpch_line_items` as Snowflake views. The intermediate layer creates `int_order_items` and `int_order_items_summary`, which calculate item-level discount amounts and aggregate gross sales and discounts by order. The mart layer creates `fct_orders` as a table containing order attributes together with summarized item-level measures.

Data quality checks include uniqueness and non-null constraints for key columns, relationship checks between orders and line items, accepted values for order status (`P`, `O`, and `F`), validation of order dates, and a check that calculated discount amounts are not positive.

## Prerequisites

You need a Snowflake account with access to the `SNOWFLAKE_SAMPLE_DATA.TPCH_SF1` schema, Python, and a local dbt installation with the Snowflake adapter. You also need a dbt profile named `data_pipeline` configured with your Snowflake account, user, role, warehouse, database, schema, and authentication method. Keep credentials in your local dbt profile or environment variables rather than committing them to GitHub.

## Running the project

From the project directory, install the declared dbt package and run the models:

```bash
dbt deps
dbt debug
dbt run
dbt test
```

To rebuild the project from a clean state, use:

```bash
dbt clean
dbt deps
dbt run
dbt test
```

The staging models are configured as Snowflake views, while the mart models are configured as Snowflake tables and use the `dbt_wh` warehouse setting. These defaults can be adjusted in `dbt_project.yml` or overridden in individual model configurations.

## Tutorial reference

This project was completed by referring to a YouTube tutorial on building a Snowflake and dbt data pipeline. 

The project is an educational implementation and may contain adaptations made while following the tutorial.

## License

This repository is intended for learning and demonstration purposes.

## References

[1]: https://docs.getdbt.com/docs/introduction "dbt Documentation"
[2]: https://docs.snowflake.com/en/user-guide/sample-data-tpch "Snowflake TPC-H Sample Data"

- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](https://community.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices
