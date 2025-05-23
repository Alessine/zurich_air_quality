# End-to-end Data Pipeline: Zurich Air Quality Data

![photo of a city in the smog](./img/smog.jpg)

Photo by [Call Me Fred](https://unsplash.com/de/@callmefred) on [Unsplash](https://unsplash.com/de/fotos/luftaufnahme-der-stadt-uDLtqbbVR4I)

## Project Overview

This project is my final submission for the [Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp) (cohort 2025). I built a batch data pipeline that automatically updates daily, leveraging modern cloud and containerization technologies. The data used in this project is provided by the City of Zurich with an open source Creative Commons License (cc-zero).

## Problem Statement

Air pollution is a serious public health issue in many cities around the world. I discovered years ago that my city publishes all of its measurements of air pollutants, but I didn't know how to make sense of this data. I wanted to create a visualization that gives a clear indication of how serious a certain level of pollution is, for each independent pollutant, and which also explains what this pollutant does. 

Therefore, I generated a dataset that contains the pollution measurements, but also emission limits and descriptions for each pollutant. For the visualization I then chose to calculate how many days in a year the pollution was higher than it should have been. This gives an intuitive understanding of how damaging the situation is.

## Technologies Used

The project utilizes the following technologies:

- **Docker & Docker Compose**: For containerization and orchestration ([Docker](https://www.docker.com/), [Docker Compose](https://docs.docker.com/compose/))
- **Google Cloud Virtual Machine (VM) with Scheduling**: To run automated data workflows ([Google Compute Engine](https://cloud.google.com/compute))
- **Kestra**: Orchestrating workflows in a scalable manner ([Kestra](https://kestra.io/))
- **Google Cloud Storage**: Storing raw data files ([Google Cloud Storage](https://cloud.google.com/storage))
- **BigQuery**: Cloud-based data warehouse for storing and querying data ([BigQuery](https://cloud.google.com/bigquery))
- **Dataform**: Automated data transformations (modeling) in BigQuery ([Dataform](https://cloud.google.com/dataform))
- **Looker Studio**: Creating visualizations and reports ([Looker Studio](https://cloud.google.com/looker-studio))
- **GitHub**: Version control and project management

## Project Architecture

Below is an illustration of the different steps in the data pipeline and the tools used to complete each one:

![screenshot of project architecture](./img/architecture_screenshot.png)

In the following sections, there's detailed information on how each of these steps was implemented.

### Data Extraction and Ingestion

The [raw data files](https://data.stadt-zuerich.ch/dataset/ugz_luftschadstoffmessung_tageswerte) are provided by the City of Zurich in their Open Data Catalog. To extract these automatically, I set up a Docker container with a Kestra image using this [docker-compose file](./docker-compose.yml) and hosted it in a virtual machine in the Google Cloud. I put the VM on a schedule to optimize the cost and used the following start-up script to start up the container inside the VM:

```bash
#! /bin/bash
apt update
docker compose -f /home/angelakniederberger/docker-compose.yml start
```

In Kestra, I scheduled three [flows](./flows/prod): to set the key-value pairs for Google Cloud authentication and extract (1) the air pollution metrics and (2) the metadata on air pollutants, emission limits and data collection locations. After downloading the data from the Open Data Catalog, it gets saved to Google Cloud Storage and from there loaded into BigQuery tables. 

For the air quality metrics, which are available in `.csv` format ([sample data](./data/ugz_ogd_air_d1_2025.csv)), the data then gets loaded into one BigQuery table incrementally (day by day). The metadata is provided in a nested `.json` format ([sample data](./data/uzg_ogd_metadaten.json)), for which I added a python transform into the Kestra flow to unnest and store it in separate `.csv` files. These are then also loaded into BigQuery tables as snapshot datasets.

### Data Transformation

Once the data is available in BigQuery, it gets transformed and modeled into tables specifically designed to fit the charts on the dashboard. I decided to use Dataform for this purpose, since it is native to the Google Cloud and integrates seamlessly with BigQuery. The data transformations are written into [SQL files](./definitions), which make up the data model. Below is a screenshot of the data lineage:

![graph of the data model in dataform](./img/dataform_screenshot.png)

The very first nodes on the graph are views, which I then materialized into a joined staging table in the next step. Wherever possible, I partitioned the tables by date and clustered them by location, because these dimensions are frequently used in `WHERE` or `GROUP BY` clauses.

### Data Storage & Querying

All the nodes on the graph above are available for querying as tables or views in BigQuery. This makes it easier to fulfill ad hoc reporting requirements. You can check all the datasets here:
- [air_quality_data](https://console.cloud.google.com/bigquery?inv=1&invt=AbtiEQ&project=zurich-air-quality&ws=!1m4!1m3!3m2!1szurich-air-quality!2sair_quality_data): tables as ingested by Kestra in production
- [air_quality_data_dev](https://console.cloud.google.com/bigquery?inv=1&invt=AbtiEQ&project=zurich-air-quality&ws=!1m4!1m3!3m2!1szurich-air-quality!2sair_quality_data_dev): tables as ingested by Kestra in development
- [dataform](https://console.cloud.google.com/bigquery?inv=1&invt=AbtiEQ&project=zurich-air-quality&ws=!1m4!1m3!3m2!1szurich-air-quality!2sdataform): views and tables generated by the dataform model in production
- [dataform_transforms_dev](https://console.cloud.google.com/bigquery?inv=1&invt=AbtiEQ&project=zurich-air-quality&ws=!1m4!1m3!3m2!1szurich-air-quality!2sdataform_transforms_dev): views and tables generated by the dataform model in development

### Visualization & Reporting

Finally, I connected LookerStudio to BigQuery via Connected Sheets. I introduced this additional step to limit the query costs to a minimum. In the sheet, I scheduled a data extract from BigQuery once per day (which gets billed), but then the connection between the sheet and LookerStudio is free, so it doesn't matter how many people use the dashboard or how often the filters are adjusted. Below is a screenshot of the dashboard.

![screenshot of the LookerStudio dashboard](./img/lookerstudio_screenshot.png)

The dashboard can be accessed [here](https://lookerstudio.google.com/reporting/f96397ee-34dc-4b35-a1e6-bcc93d6708ef).

### Version Control and Productionization

Both Kestra and Dataform are connected to this git repository. This allows for a clear distinction between development and production code. In Kestra, I set up [flows](./flows/system) that let me sync the files from GitHub, while on Dataform, I scheduled a fresh compilation of the production code once each day.

## Setup and Deployment

### Prerequisites

Ensure you have the following:

- Docker & Docker Compose
- Google Cloud Platform account with billing enabled
- GitHub account

### Steps to Deploy

1. **Clone the repository**:
   - Download all the project files by running:
      ```bash
      git clone https://github.com/Alessine/zurich_air_quality.git
      ```
3. **Setup Google Cloud VM**:
   - Create a VM instance on Google Cloud.
   - Configure a scheduling job with a startup script to run the pipelines periodically.
4. **Run Kestra for Workflow Management**:
   - Add your GitHub and GCP credentials and run the [docker-compose.yml file](./docker-compose.yml) to deploy Kestra using Docker Compose:
     ```bash
     docker-compose up -d
     ```
   - Configure workflows to automate data ingestion and transformations with the help of these [flow files](./flows/prod).
5. **Set up Dataform for Data Modeling**:
   - Create a repository and set up a development workspace.
   - Use the [workflow settings](./workflow_settings.yaml) and [definitions](./definitions) provided to set up the data model.
   - Push the code to production, compile it and execute it to generate the BigQuery tables.
6. **Build Dashboards with Looker Studio**:
   - Connect the required BigQuery datasets with a connected sheet and set up an automatic refresh.
   - Connect LookerStudio with the sheet to create an interactive dashboard.

## Repository Structure

```
├── data/                   # Sample data files  
├── definitions/            # Dataform SQL transformations       
├── flows/                  # Kestra workflows
├── img/                    # Screenshots etc.
├── .gitignore              # Files to be ignored by git
├── LICENSE                 # MIT Open Source License
├── README.md               # Project readme file
├── docker-compose.yml      # Docker Compose configuration for Kestra
└── workflow_settings.yaml  # Dataform configurations
```

## Future Improvements

- Implement CI/CD pipelines for automated deployments.
- Enhance data quality checks and monitoring.
- Scale workflows for higher data volumes.

## Contact

For any questions or contributions, feel free to connect via [GitHub](https://github.com/Alessine) or [LinkedIn](https://www.linkedin.com/in/angela-niederberger/).

