# Weather ETL Pipeline

A simple data pipeline that extracts weather data from the Open-Meteo API, transforms it, and loads it into a PostgreSQL database using Apache Airflow.

## Project Overview

This project demonstrates an ETL (Extract, Transform, Load) pipeline that:
- Extracts current weather data for New York City from the Open-Meteo API
- Transforms the raw data into a structured format
- Loads the processed data into a PostgreSQL database
- Runs daily using Apache Airflow for orchestration

## Technologies Used

- Apache Airflow - Workflow orchestration
- PostgreSQL - Data storage
- Python - Programming language
- Docker - Containerization
- Astronomer CLI - Local Airflow development environment

## Prerequisites

- Docker Desktop installed and running
- Astronomer CLI installed
- Basic knowledge of Python and SQL
- AWS RDS PostgreSQL instance (optional, for cloud storage)

## Project Structure
```
Weather-ETL-Pipeline/
├── dags/
│   └── etlweather.py          # Main DAG file
├── requirements.txt            # Python dependencies
├── Dockerfile                  # Docker configuration
└── README.md                   # Project documentation
```

## Setup Instructions

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/weather-etl-pipeline.git
cd weather-etl-pipeline
```

### 2. Install Dependencies

Make sure your `requirements.txt` includes:
```
apache-airflow-providers-http
apache-airflow-providers-postgres
```

### 3. Start Airflow
```bash
astro dev start
```

This command will start all Airflow services including the webserver, scheduler, and PostgreSQL database.

### 4. Access Airflow UI

Open your browser and navigate to:
```
http://localhost:8080
```

Login credentials:
- Username: admin
- Password: admin

### 5. Configure Connections

#### API Connection
1. Go to Admin > Connections
2. Click the + button to add a new connection
3. Fill in the details:
   - Connection Id: `open_meteo_api`
   - Connection Type: HTTP
   - Host: `https://api.open-meteo.com`
4. Click Save

#### PostgreSQL Connection
1. Click + to add another connection
2. Fill in the details:
   - Connection Id: `postgres_default`
   - Connection Type: Postgres
   - Host: `postgres` (for local) or your RDS endpoint
   - Schema: `postgres`
   - Login: `postgres`
   - Password: `postgres`
   - Port: `5432`
3. Click Test to verify the connection
4. Click Save

### 6. Run the DAG

1. Go to the DAGs page in Airflow UI
2. Find `weather_etl_pipeline`
3. Toggle the switch to enable the DAG
4. Click the play button to trigger a manual run

## DAG Details

### Tasks

1. **extract_weather_data**: Fetches current weather data from Open-Meteo API
2. **transform_weather_data**: Processes and structures the raw weather data
3. **load_weather_data**: Inserts the transformed data into PostgreSQL

### Schedule

The pipeline runs daily at midnight (configured with `@daily` schedule).

### Data Schema

The `weather_data` table contains:
- latitude (FLOAT)
- longitude (FLOAT)
- temperature (FLOAT)
- windspeed (FLOAT)
- winddirection (FLOAT)
- weathercode (INT)
- timestamp (TIMESTAMP)

## Viewing the Data

### Option 1: Using psql (Command Line)
```bash
docker exec -it $(docker ps -q -f name=postgres) psql -U postgres
```

Then run:
```sql
SELECT * FROM weather_data ORDER BY timestamp DESC LIMIT 10;
```

### Option 2: Using pgAdmin or DBeaver

Connect to your PostgreSQL database using:
- Host: localhost
- Port: 5432
- Database: postgres
- Username: postgres
- Password: postgres

## Configuration

### Changing Location

To change the location for weather data, modify these variables in `etlweather.py`:
```python
LATITUDE = '40.7128'  # New York City
LONGITUDE = '-74.0060'
```

### Changing Schedule

To modify the schedule, update the `schedule` parameter in the DAG definition:
```python
schedule = '@daily'  # Options: @hourly, @daily, @weekly, @monthly
```

## Stopping the Pipeline

To stop all Airflow services:
```bash
astro dev stop
```

## Acknowledgments

- Open-Meteo API for providing free weather data
- Apache Airflow community for the excellent documentation
- Astronomer for the CLI tool
