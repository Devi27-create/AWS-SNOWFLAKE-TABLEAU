# AWS–Snowflake–Tableau
Energy Consumption Dashboard

## Project Overview

The Energy Consumption Dashboard is an end-to-end data analytics project that demonstrates how cloud storage, data warehousing, and business intelligence tools can be integrated to analyze global energy usage. The project leverages AWS S3, Snowflake, and Tableau to explore energy consumption (KWH) and cost savings (CSU) across countries, regions, and energy sources.

This solution enables users to interactively identify energy trends, compare regions, and evaluate the impact of different energy sources on consumption and savings.

## Architecture

CSV Dataset → AWS S3 → Snowflake (Staging & Transformations) → Tableau Dashboard

## Tech Stack

AWS S3 – Cloud storage for raw datasets

Snowflake – Data warehousing, staging, and transformations

Tableau – Interactive dashboards and visual analytics

SQL – Data loading and transformation logic

## Data Source

Udemy – Complete Data Analyst Bootcamp
https://www.udemy.com/course/complete-data-analyst-bootcamp-from-basics-to-advanced/

## Data Pipeline Overview
**1. Data Ingestion (AWS S3)**

Created an S3 bucket to store the raw CSV dataset.

Uploaded energy consumption data to AWS for scalable storage.

**2. Snowflake Integration**

Configured AWS IAM Role and Snowflake Storage Integration.

Established a secure connection between Snowflake and S3.

Created stages to load data directly from S3 into Snowflake tables.

**3. Data Modeling & Transformations**

Loaded raw data into Snowflake tables.

Created a transformed table to preserve the original dataset.

Applied income-based adjustments to:

Monthly Energy Usage (KWH)

Cost Savings (CSU)

**4. Visualization (Tableau)**

Connected Tableau directly to Snowflake.

Built an interactive dashboard for energy analysis.

## Key Transformations

Monthly Usage (KWH) Adjustments

Low income: +10%

Middle income: +20%

High income: +30%

Cost Savings (CSU) Adjustments

Low income: −10%

Middle income: −20%

High income: −30%

These transformations simulate real-world consumption and savings variations based on income levels.

## Dashboard Features

KWH by Country

CSU by Country

Top 6 Regions by KWH

Top 6 Regions by CSU

Top 5 Energy Sources by KWH

Top 5 Energy Sources by CSU

Interactive filtering by region and energy source

## Dashboard Objectives

Analyze global energy consumption patterns

Identify high-consumption countries and regions

Compare performance across energy sources

Support insights for sustainability and energy planning

## Dashboard Preview

See what the dashboard looks like - ![Alt Text](https://github.com/Devi27-create/AWS-SNOWFLAKE-TABLEAU/blob/main/Energy_Consumption_dashboard.png)

## Repository Structure

├── sql/
│   ├── create_tables.sql
│   ├── storage_integration.sql
│   ├── transformations.sql
├── docs/
│   ├── aws_setup.md
│   ├── snowflake_setup.md
│   └── tableau_guide.md
├── dashboard/
│   └── Energy_Consumption.twb
└── README.md





