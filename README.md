# Real-Time Fraud Detection Data Pipeline on GCP

This project implements an end-to-end, low-latency data pipeline on Google Cloud Platform (GCP) for real-time fraud detection. The system ingests high-volume event streams, processes them in real-time to perform model inference, and stores the enriched data in an analytics-ready format.

## Table of Contents
- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Technologies Used](#technologies-used)
- [Setup and Installation](#setup-and-installation)
- [Usage](#usage)
- [Future Improvements](#future-improvements)

## Project Overview

The primary goal of this project is to detect fraudulent transactions or events as they happen. This is achieved by building a scalable and efficient data pipeline that leverages modern data engineering tools. The pipeline consists of a real-time (streaming) component for immediate detection and a batch component for complex analytics and model retraining.

## Architecture

The architecture is built entirely on GCP and is divided into three main stages:

1.  **Data Ingestion & Real-Time Processing:**
    *   **Pub/Sub:** Ingests high-throughput event data from various sources.
    *   **Apache Beam (on Dataflow):** A streaming pipeline consumes messages from Pub/Sub. For each message, it performs:
        *   Data validation and parsing.
        *   Enrichment with data from sources like Firestore.
        *   Real-time inference using a pre-trained fraud detection model (deployed on Cloud Run, for example).
    *   **Google Cloud Storage (GCS):** The enriched and scored data is written to a data lake landing zone as hourly-partitioned Apache Parquet files.

2.  **Batch Processing & Data Warehousing:**
    *   **Apache Spark (on Dataproc):** A scheduled batch ETL job runs periodically.
    *   It reads the partitioned Parquet files from the GCS data lake.
    *   It performs transformations, aggregations, and cleaning to prepare the data for analytics.
    *   **BigQuery:** The transformed data is loaded into curated, analytics-ready tables in our data warehouse.

3.  **Orchestration:**
    *   **Cloud Composer (Apache Airflow):** Orchestrates the entire batch ETL pipeline, managing dependencies, scheduling, and retries.

![Architecture Diagram](link-to-your-architecture-diagram.png)
*(Note: It is highly recommended to create and add an architecture diagram here)*

## Technologies Used

-   **Cloud Provider:** Google Cloud Platform (GCP)
-   **Data Ingestion:** `Pub/Sub`
-   **Stream Processing:** `Apache Beam`, `Dataflow`
-   **Batch Processing:** `Apache Spark`, `Dataproc`
-   **Data Lake:** `Google Cloud Storage (GCS)`
-   **Data Warehouse:** `BigQuery`
-   **Real-time Database:** `Firestore` (for data enrichment)
-   **Model Serving:** `Cloud Run`
-   **Orchestration:** `Cloud Composer (Apache Airflow)`
-   **Containerization:** `Docker`

## Setup and Installation

To set up this project in your own GCP environment, follow these steps:

1.  **Prerequisites:**
    *   Google Cloud SDK installed and configured.
    *   Terraform or necessary permissions to create resources manually.

2.  **Clone the repository:**
    ```bash
    git clone [your-repository-link]
    cd [repository-directory]
    ```

3.  **Provision GCP Resources:**
    *   Enable necessary GCP APIs (Dataflow, Pub/Sub, BigQuery, etc.).
    *   Create a Pub/Sub topic.
    *   Create GCS buckets for the data lake and staging.
    *   Create a BigQuery dataset and tables.
    *   Set up a Dataproc cluster or use serverless Spark.
    *   Deploy the inference model to Cloud Run.

4.  **Configure Environment Variables:**
    *   Create a `.env` file and populate it with your GCP Project ID, bucket names, etc.

5.  **Deploy the Pipelines:**
    *   Deploy the Apache Beam streaming pipeline to Dataflow.
    *   Deploy the Spark ETL script to Dataproc and the Airflow DAG to Cloud Composer.

## Usage

1.  **Publishing Events:**
    *   Publish a sample JSON event to the Pub/Sub topic using the gcloud CLI or a client script.
    *   Example:
        ```bash
        gcloud pubsub topics publish [YOUR_TOPIC_ID] --message '{"transaction_id": "xyz", "amount": 150.00, "user_id": "abc"}'
        ```

2.  **Monitoring:**
    *   Observe the Dataflow job graph to see the real-time processing.
    *   Check the GCS bucket for the creation of new Parquet files.
    *   Query the BigQuery tables after the batch pipeline has run to see the transformed data.

## Future Improvements

-   **CI/CD Pipeline:** Implement a CI/CD pipeline using Cloud Build or GitHub Actions to automate testing and deployment.
-   **Model Retraining:** Build an automated model retraining pipeline that uses the curated data in BigQuery.
-   **Enhanced Monitoring:** Integrate detailed monitoring and alerting using Cloud Monitoring to track pipeline health and data quality.
