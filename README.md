# Paris 2024 Olympics – End-to-End Azure Data Pipeline

A portfolio project I built to learn the Azure data stack end to end, using Paris 2024 Olympics data as the subject. Data Factory ingests the source files, Databricks cleans and joins them, Synapse serves them as SQL, and Power BI sits on top.

![Pipeline architecture](Pipeline.png)

## The pipeline

| Stage | Service | What it does |
|---|---|---|
| Ingestion | Azure Data Factory | Copy Data activities pull each dataset (athletes, coaches, medals, teams, entries by gender) from GitHub into the raw zone, in dependency order |
| Raw storage | Data Lake Storage Gen2 | Raw files kept untouched so transformations stay reproducible |
| Transformation | Azure Databricks (PySpark, SQL) | Fix invalid IDs and country codes, standardise names, join athletes to teams and medals, derive participation metrics |
| Curated storage | Data Lake Storage Gen2 | Transformed zone, separate from raw |
| Query layer | Azure Synapse Analytics (serverless SQL) | External tables and views over the curated files: medal tally, participation summary, teams by country |
| Visualisation | Power BI | Medals by country (map), participation by sport and gender, medal distribution, teams by country |

Data Factory ran in Southeast Asia and Databricks plus the data lake in Canada Central. That was deliberate: I wanted to see what cross-region latency and egress actually looked like in a pipeline rather than read about it.

![Power BI dashboard](Dashboard.png)

## Data

About 90% of the data is synthetic (generated to look like athlete and coach records) and about 10% is real, from Kaggle (medal and team data). The project was built before the Games, so the synthetic portion stood in for data that didn't exist yet. The CSVs are in this repository.

## What's here

| File | What it is |
|---|---|
| `Athletes.csv`, `Coaches.csv`, `Medals.csv`, `teams.csv`, `EntriesGender.csv` | Source datasets |
| `Pipeline.png` | Architecture diagram |
| `Dashboard.png` | Power BI screenshot |
| `Olympic Pipeline.pptx` | Short walkthrough deck |
| `Paris 2024 Olympics Data Analysis – End-to-End Azure Analytics Solution.pdf` | Full write-up of each stage |

## What isn't here

The Azure resources were removed after the project to stop the billing, and I didn't keep the Data Factory pipeline JSON or the Databricks notebooks. The PDF documents what each stage did and how it was configured. If I rebuild this, exporting the pipeline definitions into the repo is the first thing I'd do differently.

## What I got out of it

- How Data Factory, Databricks and Synapse hand off to each other, and where the handoffs break
- Keeping raw and transformed data separate so a bad transformation can be rerun without re-ingesting
- Serverless Synapse over curated parquet/CSV as a cheap query layer instead of a dedicated pool
- The practical cost of putting services in different regions
- 
