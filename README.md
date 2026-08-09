# Parkinsons Real-Time Analytics

This repository implements an end-to-end proof-of-concept for real-time analytics on Parkinson's disease data using Snowflake (streams + ingestion), Python (Jupyter notebooks), and Streamlit dashboards. It includes data ingestion and simulation, SQL-based preprocessing/feature engineering, exploratory cloud analytics queries, Streamlit dashboards that read from Snowflake, and a notebook for predictive modelling.

**Contents**
- **Project root**: overview and landing README ([README.md](README.md)).
- **Real - Time Data Pipeline**: data ingestion and Snowflake Stream examples ([Real_Time_Data_Pipeline.ipynb](Real%20-%20Time%20Data%20Pipeline/Real_Time_Data_Pipeline.ipynb), [Snowflake - Creating a STREAM.sql](Real%20-%20Time%20Data%20Pipeline/Snowflake%20-%20Creating%20a%20STREAM.sql)).
- **Data Preprocessing**: Snowflake SQL for cleaning, transforming and feature engineering ([Snowflake - Data Cleaning.sql](Data%20Preprocessing/Snowflake%20-%20Data%20Cleaning.sql), [Snowflake - Data Transformation.sql](Data%20Preprocessing/Snowflake%20-%20Data%20Transformation.sql), [Snowflake - Feature Engineering.sql](Data%20Preprocessing/Snowflake%20-%20Feature%20Engineering.sql)).
- **Predictive Analytics**: Jupyter notebook with model training and analysis ([Predictive_Analysis.ipynb](Predictive%20Analytics/Predictive_Analysis.ipynb)).
- **Dashboards and Streamlit**: two Streamlit apps that query Snowflake ([Snowflake - Streamlit - Real time patient monitoring dashboard.py](Dashboards%20and%20Streamlit/Snowflake%20-%20Streamlit%20-%20Real%20time%20patient%20monitoring%20dashboard.py), [Snowflake - Streamlit - Parkinson's disease dashboard from Preprocessed table.py](Dashboards%20and%20Streamlit/Snowflake%20-%20Streamlit%20-%20Parkinson's%20disease%20dashboard%20from%20Preprocessed%20table.py), plus supporting dashboard SQL).
- **Cloud Analytics**: example reporting SQL files used to drive charts and analyses (folder: [Cloud Analytics](Cloud%20Analytics)).

**High-level workflow**
1. Simulate/generate patient events and ingest them into a Snowflake table (`TEST1`) using the notebook in [Real - Time Data Pipeline](Real%20-%20Time%20Data%20Pipeline/Real_Time_Data_Pipeline.ipynb).
2. Create a Snowflake `STREAM` on the base table to support real-time change capture (see [Snowflake - Creating a STREAM.sql](Real%20-%20Time%20Data%20Pipeline/Snowflake%20-%20Creating%20a%20STREAM.sql)).
3. Run the SQL preprocessing steps to create cleaned, transformed and feature-engineered tables: `TEST1_CLEANED`, `TEST1_TRANSFORMED`, and `TEST1_FEATUREENG` (see files in [Data Preprocessing](Data%20Preprocessing)).
4. Use the Streamlit apps in [Dashboards and Streamlit](Dashboards%20and%20Streamlit) to visualize live and aggregated metrics by querying the Snowflake tables via Snowpark.
5. Use the predictive notebook ([Predictive_Analysis.ipynb](Predictive%20Analytics/Predictive_Analysis.ipynb)) to run ML experiments on the feature-engineered table.

**Key Snowflake objects and assumptions**
- Database/Schema used in SQL and notebooks: database `TEST1`, schema `STREAM` (e.g. `TEST1.STREAM.TEST1`).
- Tables created/expected: `TEST1` (raw ingest), `TEST1_CLEANED`, `TEST1_TRANSFORMED`, `TEST1_FEATUREENG`.
- Streaming: a Snowflake `STREAM` is created on `TEST1` (see `Snowflake - Creating a STREAM.sql`).
- The Streamlit apps assume a working Snowpark `Session` accessible via `get_active_session()` in the environment where Streamlit runs.

Prerequisites
- Python 3.8+ (the notebooks indicate Python 3.12 compatibility but 3.8+ is typical).
- A Snowflake account with a database, schema and a user that can create tables, stages, streams and run queries.
- Required Python packages (install in a virtualenv); common packages used across notebooks and apps:

```bash
pip install streamlit pandas scikit-learn xgboost snowflake-connector-python snowflake-ingest "snowflake-snowpark-python" python-dotenv
```

Configuration and credentials
- Do NOT commit credentials. The notebooks currently show example/hard-coded credentials; replace them with secure methods.
- For Streamlit + Snowpark apps, configure Snowflake authentication by one of:
	- setting environment variables and initializing a Snowpark `Session` before calling `get_active_session()`;
	- using a secrets manager or Snowpark-native auth (key-pair, OAuth) as recommended by Snowflake.

- The notebooks now load environment variables from a local `.env` file (if present) using `python-dotenv`.
	- Copy `env.example` to `.env` and fill in your Snowflake credentials before running the notebooks locally.
	- `env.example` is provided in the repository as a template; do NOT commit your real `.env` file.
	- Alternatively, set the environment variables directly in your shell or CI environment: `SNOW_USER`, `SNOW_PASSWORD`, `SNOW_ACCOUNT`, and optional `SNOW_WAREHOUSE`, `SNOW_DATABASE`, `SNOW_SCHEMA`.

Note: this repository now includes `requirements.txt` and `env.example` to simplify setup.

Running the Streamlit dashboards
- Real-time monitoring dashboard:

```bash
streamlit run "Dashboards and Streamlit/Snowflake - Streamlit - Real time patient monitoring dashboard.py"
```

- Parkinson's disease dashboard (feature-engineered view):

```bash
streamlit run "Dashboards and Streamlit/Snowflake - Streamlit - Parkinson's disease dashboard from Preprocessed table.py"
```

Running the pipeline notebook
- Open and run [Real - Time Data Pipeline/Real_Time_Data_Pipeline.ipynb] to simulate ingestion into `TEST1`. Confirm the notebook uses secure credentials before running.

Notes on predictive modelling
- The notebook in [Predictive Analytics](Predictive%20Analytics/Predictive_Analysis.ipynb) installs and uses common ML libraries (scikit-learn, xgboost). It expects the feature-engineered table `TEST1_FEATUREENG` to be present in Snowflake and pulls data into pandas for training.

Security and privacy
- This repository contains simulated patient-like records for demonstration. Treat any real patient data according to applicable privacy laws and organizational policies.
- Replace hard-coded secrets with environment-based or secret-store approaches before using on real data.

Limitations and assumptions
- The codebase is a proof-of-concept: not production hardened. It lacks structured error handling, CI, and deployment automation.
- Snowpark `get_active_session()` is used in Streamlit apps and requires the Snowpark session to be created by the execution environment (see Snowflake/Snowpark docs).

Where to look next
- Start by reviewing and adapting the pipeline notebook: [Real_Time_Data_Pipeline.ipynb](Real%20-%20Time%20Data%20Pipeline/Real_Time_Data_Pipeline.ipynb).
- Run the Data Preprocessing SQL in Snowflake to create `TEST1_FEATUREENG` before launching the dashboards.
- If you want, I can:
	- add a `requirements.txt` and `env.example` for credentials,
	- refactor Streamlit apps to create the Snowpark `Session` from environment variables,
	- containerize the dashboards for repeatable deployment.

License
- See [LICENSE](LICENSE) for license information.

---
If you'd like, I can now create `requirements.txt`, secure the notebooks by removing hard-coded credentials, and refactor the Streamlit apps to support environment-based Snowpark connection. Which would you prefer next?
