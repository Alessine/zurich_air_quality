<h1>Air Pollutants in Zurich, Switzerland (Data Engineering Zoomcamp Final Project)</h1>

<h2>Project Overview</h2>

This project is the final submission for my Data Engineering course, where I transitioned from a Data Analyst role to mastering core Data Engineering skills. The project involves building a robust data pipeline leveraging modern cloud and containerization technologies.

Technologies Used

The project utilizes the following technologies:

Docker & Docker Compose: For containerization and orchestration (Docker, Docker Compose).

Google Cloud Virtual Machine (VM) with Scheduling: To run automated data workflows (Google Compute Engine).

Kestra: Orchestrating workflows in a scalable manner (Kestra).

BigQuery: Cloud-based data warehouse for storing and querying data (BigQuery).

Dataform: Managing and transforming datasets in BigQuery (Dataform).

Looker Studio: Creating visualizations and reports (Looker Studio).

GitHub: Version control and project management (GitHub).

Project Architecture

The project follows a structured data pipeline:

Data Extraction: Extracts raw data from a source API or dataset.

Data Ingestion: Uses Dockerized containers to process and upload data to Google Cloud Storage.

Orchestration: Kestra schedules and triggers workflows to process data.

Data Transformation: Dataform is used to apply transformations in BigQuery.

Data Storage & Querying: Processed data is stored in BigQuery for analytical queries.

Visualization & Reporting: Looker Studio connects to BigQuery to generate reports and dashboards.

Setup and Deployment

Prerequisites

Ensure you have the following installed:

Docker & Docker Compose

Google Cloud SDK

GitHub account with repository access

Steps to Deploy

Clone the repository:

git clone https://github.com/your-username/your-repository.git
cd your-repository

Setup Google Cloud VM:

Create a VM instance on Google Cloud.

Configure a scheduling job to run scripts periodically.

Install necessary dependencies (Docker, Kestra, BigQuery SDK, etc.).

Run Kestra for Workflow Management:

Deploy Kestra using Docker Compose:

docker-compose up -d

Configure workflows to automate data ingestion and transformations.

Integrate with BigQuery:

Set up datasets and tables in BigQuery.

Deploy Dataform transformations.

Build Dashboards with Looker Studio:

Connect Looker Studio to BigQuery.

Create interactive dashboards.

Repository Structure

├── dags/                 # Kestra workflow definitions
├── data/                 # Raw and processed data files
├── sql/                  # Dataform SQL transformations
├── docker-compose.yml    # Docker Compose configuration
├── scripts/              # Automation and processing scripts
└── README.md             # Project documentation

Future Improvements

Implement CI/CD pipelines for automated deployments.

Enhance data quality checks and monitoring.

Scale workflows for higher data volumes.

Contact

For any questions or contributions, feel free to connect via GitHub.

