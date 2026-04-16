# Day-10-Initial-Lakehouse-setup-and-Data-Pipeline
Microsoft Fabric: End-to-End NYC Taxi Data Project
Welcome to my Microsoft Fabric project! This repository documents my journey building an end-to-end data engineering solution using the NYC Taxi dataset. This project focuses on Orchestration, Data Transformation, and Metadata Management within the Fabric ecosystem.

🚀 Project Overview
The goal is to move raw NYC Taxi data through a multi-stage architecture:

Staging Layer: Ingesting raw Parquet/CSV files and cleaning outliers.

Presentation Layer: Transforming data into a business-ready format (Gold layer).

Metadata Management: Tracking every pipeline run for full observability.

🏗️ Pipeline Architecture
1. Staging Pipeline (pl_stg_processing_nyctaxi)
This pipeline handles the initial ingestion and incremental loading logic.

Key Logic implemented:

Lookup Activity: Retrieves the latest_processed_pickup from the metadata.processing_log.

Dynamic Variables: Uses @formatDateTime to dynamically calculate the next month's data to process (v_date and v_end_date).

Data Cleaning: A Stored Procedure stg.data_cleaning_stg removes outlier dates that fall outside the expected month.

SQL
-- Logic used to remove outliers
CREATE PROCEDURE stg.data_cleaning_stg
@end_date datetime2, @start_date datetime2
AS
DELETE FROM stg.nyctaxi_yellow 
WHERE tpep_pickup_datetime < @start_date OR tpep_pickup_datetime > @end_date;
2. Presentation Pipeline (pl_pres_processing_nyctaxi)
This pipeline transforms the cleaned staging data into a user-friendly format.

Key Transformations:

Vendor Mapping: Converts IDs (1, 2) into readable names like 'Creative Mobile Technologies' or 'VeriFone'.

Payment Normalization: Maps payment type codes to 'Credit Card', 'Cash', etc.

Spatial Joins: Joins the taxi data with taxi_zone_lookup to bring in Borough and Zone names.

SQL
-- Presentation Logic Snapshot
CASE 
    WHEN nty.payment_type = 1 THEN 'Credit Card'
    WHEN nty.payment_type = 2 THEN 'Cash'
    ELSE 'Unknown'
END AS payment_method
📊 Metadata & Observability
I implemented a robust logging system using a dedicated metadata schema. Every time a pipeline runs, the following table is updated:

SQL
CREATE TABLE metadata.processing_log (
    pipeline_run_id varchar(255), 
    table_processed varchar(255), 
    rows_processed INT, 
    latest_processed_pickup datetime2(6),
    processed_datetime datetime2(6)
);
🛠️ Tech Stack
Platform: Microsoft Fabric

Engine: Synapse Data Engineering (Spark & SQL)

Orchestration: Data Factory Pipelines

Storage: OneLake (Delta Lake format)

Language: T-SQL, Spark SQL

📈 Build in Public Progress
[x] Day 10: Setup Lakehouse and GitHub Integration.

[x] Day 11: Configured Staging Pipeline and Metadata logging.

[ ] Day 12: Build Power BI Report on top of the Presentation Layer.
