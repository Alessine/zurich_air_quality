# Data Engineering Final Project

## Project Overview

This project is the final submission for my Data Engineering course, where I transitioned from a Data Analyst role to mastering core Data Engineering skills. The project involves building a robust data pipeline leveraging modern cloud and containerization technologies.

## Technologies Used

The project utilizes the following technologies:

- **Docker & Docker Compose**: For containerization and orchestration ([Docker](https://www.docker.com/), [Docker Compose](https://docs.docker.com/compose/)).
- **Google Cloud Virtual Machine (VM) with Scheduling**: To run automated data workflows ([Google Compute Engine](https://cloud.google.com/compute)).
- **Kestra**: Orchestrating workflows in a scalable manner ([Kestra](https://kestra.io/)).
- **BigQuery**: Cloud-based data warehouse for storing and querying data ([BigQuery](https://cloud.google.com/bigquery)).
- **Dataform**: Managing and transforming datasets in BigQuery ([Dataform](https://dataform.co/)).
- **Looker Studio**: Creating visualizations and reports ([Looker Studio](https://lookerstudio.google.com/)).
- **GitHub**: Version control and project management ([GitHub](https://github.com/)).

## Project Architecture

The project follows a structured data pipeline:

1. **Data Extraction**: Extracts raw data from a source API or dataset.
2. **Data Ingestion**: Uses Dockerized containers to process and upload data to Google Cloud Storage.
3. **Orchestration**: Kestra schedules and triggers workflows to process data.
4. **Data Transformation**: Dataform is used to apply transformations in BigQuery.
5. **Data Storage & Querying**: Processed data is stored in BigQuery for analytical queries.
6. **Visualization & Reporting**: Looker Studio connects to BigQuery to generate reports and dashboards.

## Setup and Deployment

### Prerequisites

Ensure you have the following installed:

- Docker & Docker Compose
- Google Cloud SDK
- GitHub account with repository access

### Steps to Deploy

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/your-repository.git
   cd your-repository
   ```
2. **Setup Google Cloud VM**:
   - Create a VM instance on Google Cloud.
   - Configure a scheduling job to run scripts periodically.
   - Install necessary dependencies (Docker, Kestra, BigQuery SDK, etc.).
3. **Run Kestra for Workflow Management**:
   - Deploy Kestra using Docker Compose:
     ```bash
     docker-compose up -d
     ```
   - Configure workflows to automate data ingestion and transformations.
4. **Integrate with BigQuery**:
   - Set up datasets and tables in BigQuery.
   - Deploy Dataform transformations.
5. **Build Dashboards with Looker Studio**:
   - Connect Looker Studio to BigQuery.
   - Create interactive dashboards.

## Repository Structure

```
├── dags/                 # Kestra workflow definitions
├── data/                 # Raw and processed data files
├── sql/                  # Dataform SQL transformations
├── docker-compose.yml    # Docker Compose configuration
├── scripts/              # Automation and processing scripts
└── README.md             # Project documentation
```

## Future Improvements

- Implement CI/CD pipelines for automated deployments.
- Enhance data quality checks and monitoring.
- Scale workflows for higher data volumes.

## Contact

For any questions or contributions, feel free to connect via [GitHub](https://github.com/your-username).

