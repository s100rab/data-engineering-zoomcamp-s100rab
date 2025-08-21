# End-to-End Data Engineering Pipeline: From Ingestion to Cloud Data Lake

This project demonstrates the design and implementation of a robust, automated data engineering pipeline. I handle the entire data lifecycle, from ingesting raw data and building a scalable data lake to orchestrating complex workflows with Apache Airflow and loading the data into a cloud data warehouse.

---

## 🚀 Project Overview

The goal of this project was to build a production-grade data pipeline that efficiently processes and moves large datasets. I chose to work with the NYC Taxi trip dataset, a classic big data challenge, to showcase my ability to handle real-world data engineering problems.

This repository covers the entire process, including:
* **Building a Data Lake**: Designing and implementing a central repository for raw data using Google Cloud Storage.
* **Workflow Orchestration**: Creating complex, scheduled, and dependency-aware data workflows (DAGs) with Apache Airflow.
* **Containerization**: Setting up a complete, isolated development environment for Airflow using Docker.
* **Multi-Destination Ingestion**: Engineering pipelines to load data into both a local PostgreSQL database for development and Google BigQuery for cloud-based analytics.
* **Cloud-Native Integration**: Utilizing GCP's native Transfer Service to ingest data directly from other cloud providers (AWS S3).

---

## 🛠️ Technologies & Tools

* **Orchestration**: Apache Airflow
* **Cloud Platform**: Google Cloud Platform (GCP)
    * **Data Lake**: Google Cloud Storage (GCS)
    * **Data Warehouse**: Google BigQuery
    * **Cloud-Native ETL**: GCP Transfer Service
* **Containerization**: Docker & Docker-Compose
* **Databases**: PostgreSQL
* **Data Formats**: Parquet, CSV
* **Languages**: Python, SQL
* **Infrastructure as Code**: Terraform

---

## 🏛️ Project Architecture & Workflow

The core of this project is a Directed Acyclic Graph (DAG) orchestrated by Airflow. This workflow automates the following sequence of tasks:

1.  **Download Data**: A task is triggered on a schedule to download the raw NYC Taxi trip data (CSV files) from a web source.
2.  **Data Transformation**: The downloaded CSV data is converted into **Parquet**, a highly efficient columnar storage format optimized for analytical queries.
3.  **Upload to Data Lake**: The processed Parquet file is uploaded to a **Google Cloud Storage (GCS) bucket**, which serves as our central data lake. This provides a durable, scalable, and cost-effective storage solution.
4.  **Load to Data Warehouse**: An external table is created in **Google BigQuery** that directly points to the Parquet files in our data lake. This ELT (Extract, Load, Transform) approach allows for immediate querying and analysis on the cloud without needing to manage a separate database infrastructure.
5.  **Local Ingestion (for Development)**: A parallel pipeline was also developed to ingest the same data into a local **PostgreSQL** database, demonstrating the flexibility to work with different data destinations.


---

## ✨ Key Features & Accomplishments

* **Automated & Resilient Pipelines**: Designed and implemented fully automated data ingestion workflows using **Apache Airflow**. I created custom DAGs with clear task dependencies, scheduling, and parameterization using Jinja templating, ensuring the pipelines are robust and idempotent.

* **Containerized Airflow Environment**: Built a custom, portable Airflow environment using **Docker and Docker-Compose**. This involved customizing the official Airflow image to include necessary GCP libraries and credentials, creating a reproducible and isolated setup that mirrors a production environment.

* **Data Lake & Warehouse Implementation**: Successfully built a cloud data architecture on **GCP**. I configured Google Cloud Storage as a data lake for raw and processed data and used **BigQuery** as a serverless data warehouse to run analytics, demonstrating a strong understanding of modern cloud data platforms.

* **Efficient Data Processing**: Implemented a crucial optimization step by converting raw CSV data to **Parquet**. This significantly improves storage efficiency and query performance, a key practice in big data processing.

* **Infrastructure as Code (IaC)**: Utilized **Terraform** to programmatically provision and manage cloud resources, including the GCP Transfer Service jobs. This demonstrates best practices for creating maintainable and scalable cloud infrastructure.


### Concepts

 [Airflow Concepts and Architecture](docs/1_concepts.md)

### Workflow

 ![](docs/gcs_ingestion_dag.png)
 
### Setup - Official Version
 (For the section on the Custom/Lightweight setup, scroll down)

 #### Setup
  [Airflow Setup with Docker, through official guidelines](1_setup_official.md)

 #### Execution
 
  1. Build the image (only first-time, or when there's any change in the `Dockerfile`, takes ~15 mins for the first-time):
     ```shell
     docker-compose build
     ```
   
     or (for legacy versions)
   
     ```shell
     docker build .
     ```

 2. Initialize the Airflow scheduler, DB, and other config
    ```shell
    docker-compose up airflow-init
    ```

 3. Kick up the all the services from the container:
    ```shell
    docker-compose up
    ```

 4. In another terminal, run `docker-compose ps` to see which containers are up & running (there should be 7, matching with the services in your docker-compose file).

 5. Login to Airflow web UI on `localhost:8080` with default creds: `airflow/airflow`

 6. Run your DAG on the Web Console.

 7. On finishing your run or to shut down the container/s:
    ```shell
    docker-compose down
    ```

    To stop and delete containers, delete volumes with database data, and download images, run:
    ```
    docker-compose down --volumes --rmi all
    ```

    or
    ```
    docker-compose down --volumes --remove-orphans
    ```
       
### Setup - Custom No-Frills Version (Lightweight)
This is a quick, simple & less memory-intensive setup of Airflow that works on a LocalExecutor.

  #### Setup
  [Airflow Setup with Docker, customized](2_setup_nofrills.md)

  #### Execution
  
  1. Stop and delete containers, delete volumes with database data, & downloaded images (from the previous setup):
    ```
    docker-compose down --volumes --rmi all
    ```

   or
    ```
    docker-compose down --volumes --remove-orphans
    ```
    
   Or, if you need to clear your system of any pre-cached Docker issues:
    ```
    docker system prune
    ```
    
   Also, empty the airflow `logs` directory.
    
  2. Build the image (only first-time, or when there's any change in the `Dockerfile`):
  Takes ~5-10 mins for the first-time
    ```shell
    docker-compose build
    ```
    or (for legacy versions)
    ```shell
    docker build .
    ```

  3. Kick up the all the services from the container (no need to specially initialize):
    ```shell
    docker-compose -f docker-compose-nofrills.yml up
    ```

  4. In another terminal, run `docker ps` to see which containers are up & running (there should be 3, matching with the services in your docker-compose file).

  5. Login to Airflow web UI on `localhost:8080` with creds: `admin/admin` (explicit creation of admin user was required)

  6. Run your DAG on the Web Console.

  7. On finishing your run or to shut down the container/s:
    ```shell
    docker-compose down
    ```
    
### Setup - Taken from DE Zoomcamp 2.3.4 - Optional: Lightweight Local Setup for Airflow

Use the docker-compose_2.3.4.yaml file (and rename it to docker-compose.yaml). Don't forget to replace the variables `GCP_PROJECT_ID` and `GCP_GCS_BUCKET`.

### Future Enhancements
* Deploy self-hosted Airflow setup on Kubernetes cluster, or use a Managed Airflow (Cloud Composer) service by GCP

### References
For more info, check out these official docs:
   * https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html
   * https://airflow.apache.org/docs/docker-stack/build.html
   * https://airflow.apache.org/docs/docker-stack/recipes.html

