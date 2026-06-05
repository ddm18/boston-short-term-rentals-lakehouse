# NYC Urban Mobility Data Platform

Production-like batch lakehouse for NYC taxi mobility and fare analysis.

Phase 1 implements a data engineering platform for yellow taxi trips, green taxi
trips, and TLC taxi zone reference data. It focuses on reproducible ingestion,
dbt-based lakehouse modeling, data quality controls, orchestration, and a
cloud validation slice for deployment confidence.

The business framing and customer-style requirements are documented in
[Business Requirements](docs/business-requirements.md). The architecture and
release model are documented in [System Overview](docs/architecture/overview.md)
and [CI/CD Workflow](docs/architecture/cicd.md).

For the full project documentation, architecture diagrams, ADRs, and business
context, use the MkDocs site:

https://ddm18.github.io/nyc-taxi-lakehouse/

## Phase 1 Scope

Implemented:

- TLC yellow and green taxi monthly trip ingestion
- TLC taxi zone reference bootstrap
- Landing, Bronze, Silver, Gold, Ops, and Quarantine layers
- dbt models with schema tests and contracts for analytical outputs
- Gold trip-level, daily, and hourly-zone analytical datasets
- Source metadata, transformation versioning, stage state, and reprocess signals
- Airflow orchestration for local runtime
- MWAA, ECS/Fargate, Lambda, RDS, S3, and ECR validation slice for deployed `test`
  and `prod` environments
- GitHub Actions CI/CD workflow with test/prod promotion gates
- MkDocs architecture documentation and ADRs

Roadmap, not Phase 1:

- Weather enrichment
- Airport passenger traffic enrichment
- Holidays and events enrichment
- Real-time streaming
- Machine learning or forecasting

## Repository Map

| Path | Purpose |
|---|---|
| `ingestion/` | Landing ingestion, source descriptors, source metadata, and pipeline state helpers |
| `dbt/nyc_taxi_lakehouse/` | Bronze, Silver, Gold, Ops, and Quarantine dbt models |
| `airflow/dags/nyc_taxi_pipeline.py` | Local and cloud Airflow DAG definition |
| `orchestration/cloud/` | ECS task runner, MWAA control-plane Lambda, and cloud validation helpers |
| `infra/terraform/` | Bootstrap resources plus deployed `test` and `prod` validation infrastructure |
| `docs/` | MkDocs site, architecture pages, ADRs, discovery notes, and business requirements |
| `.github/workflows/` | CI, deploy, validation, and promotion workflows |

## Analytical Outputs

Phase 1 Gold datasets include:

- `trips_v1`
- `yellow_trips_v1`
- `green_trips_v1`
- `daily_metrics_v1`
- `hourly_zone_metrics_v1`
- service-specific daily and hourly-zone aggregate models

The core semantic reference model is `dim_taxi_zones_v1`.

## Local Validation

Install runtime and documentation dependencies before running local checks:

```bash
python3 -m pip install -r airflow/requirements.txt
python3 -m pip install -r docs/requirements.txt
python3 -m unittest discover -s tests -q
terraform fmt -check -recursive infra/terraform
python3 -m mkdocs build --strict
```

Terraform validation in CI initializes bootstrap, `test`, and `prod` stacks with
`-backend=false`.

## Documentation

The published documentation site is available at:

https://ddm18.github.io/nyc-taxi-lakehouse/

Build the documentation site locally:

```bash
python3 -m pip install -r docs/requirements.txt
python3 -m mkdocs build --strict
```

The generated `site/` directory is build output and is not source.
