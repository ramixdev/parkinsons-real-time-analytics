# Parkinsons Real-Time Analytics

A proof-of-concept pipeline for real-time analytics on Parkinson’s disease data using Snowflake (streams & ingestion), Python (Jupyter), and Streamlit dashboards. This repository demonstrates event simulation and ingestion, SQL-based cleaning and feature engineering, realtime dashboards, and a predictive modelling notebook.

**Contents**
- Real-Time ingestion: `Real - Time Data Pipeline/Real_Time_Data_Pipeline.ipynb` and `Real - Time Data Pipeline/Snowflake - Creating a STREAM.sql`
- Data preprocessing: `Data Preprocessing/*.sql` (cleaning, transformation, feature engineering)
- Predictive analytics: `Predictive Analytics/Predictive_Analysis.ipynb`
- Dashboards: `Dashboards and Streamlit/*.py` (real-time and feature-engineered views)
- Cloud analytics: `Cloud Analytics/*.sql` (example reporting queries)

Key ideas
- Simulate patient events and ingest them into Snowflake table `TEST1`.
- Use a Snowflake `STREAM` on `TEST1` for change capture and downstream processing.
- Produce `TEST1_CLEANED`, `TEST1_TRANSFORMED`, and `TEST1_FEATUREENG` via SQL.
- Visualize and explore data with Streamlit apps using Snowpark/Snowflake.
- Train ML models locally by pulling `TEST1_FEATUREENG` into pandas.

## Prerequisites
- Python 3.8+ (3.8–3.12 tested). Use a virtual environment.
- A Snowflake account with privileges to create tables, stages, streams and run queries.
- Install dependencies in a virtualenv (see `requirements.txt`):

```bash
python -m venv .venv
source .venv/bin/activate    # macOS / Linux
.venv\Scripts\activate     # Windows
pip install -r requirements.txt
```

## Quickstart (local)
1. Copy the environment template and fill in credentials:

```bash
cp env.example .env
# edit .env and set SNOW_USER, SNOW_PASSWORD, SNOW_ACCOUNT, etc.
```

Example `.env` variables (see `env.example`):

```env
SNOW_USER=your_user
SNOW_PASSWORD=your_password
SNOW_ACCOUNT=xy12345.us-east-1
SNOW_WAREHOUSE=COMPUTE_WH
SNOW_DATABASE=TEST1
SNOW_SCHEMA=STREAM
```

2. Run notebooks
- Open and run `Real - Time Data Pipeline/Real_Time_Data_Pipeline.ipynb` to simulate events and ingest into `TEST1`.
- Run preprocessing SQL (`Data Preprocessing/*.sql`) in Snowflake to build `TEST1_FEATUREENG`.
- Run `Predictive Analytics/Predictive_Analysis.ipynb` to experiment with models using `TEST1_FEATUREENG`.

3. Run dashboards
- Real-time monitoring:

```bash
streamlit run "Dashboards and Streamlit/Snowflake - Streamlit - Real time patient monitoring dashboard.py"
```

- Feature-engineered dashboard:

```bash
streamlit run "Dashboards and Streamlit/Snowflake - Streamlit - Parkinson's disease dashboard from Preprocessed table.py"
```

## Configuration & credentials
- Do NOT commit secrets. Use `.env` locally, or a secrets manager in production.
- Notebooks were updated to optionally load environment variables via `python-dotenv`.
- `env.example` shows the required variables — do not commit your real `.env`.

## Recommended Snowpark initialization (for Streamlit)
If you want the Streamlit apps to create a Snowpark `Session` from env vars (recommended for local runs), centralize connection logic. Example:

```python
from dotenv import load_dotenv
import os
from snowflake.snowpark import Session

load_dotenv()

def make_session():
	connection_parameters = {
		"user": os.getenv("SNOW_USER"),
		"password": os.getenv("SNOW_PASSWORD"),
		"account": os.getenv("SNOW_ACCOUNT"),
		"warehouse": os.getenv("SNOW_WAREHOUSE", "COMPUTE_WH"),
		"database": os.getenv("SNOW_DATABASE", "TEST1"),
		"schema": os.getenv("SNOW_SCHEMA", "STREAM"),
	}
	return Session.builder.configs(connection_parameters).create()
```

## Security & privacy
- Data in this repository is simulated. Treat any real patient data according to applicable laws and policies.
- Replace hard-coded credentials with environment-based authentication or key-pair/OAuth as appropriate for production.

## Project status & limitations
- Proof-of-concept; not production hardened.
- Missing: automated CI, structured error handling, robust input validation, and deployment automation.
- Streamlit apps currently assume a Snowpark session may be provided by the environment (`get_active_session()`); refactoring to initialize the session locally is recommended (example above).

## Files of interest
- `Real - Time Data Pipeline/Real_Time_Data_Pipeline.ipynb`
- `Data Preprocessing/Snowflake - Feature Engineering.sql`
- `Predictive Analytics/Predictive_Analysis.ipynb`
- `Dashboards and Streamlit/*.py`
- `env.example`
- `requirements.txt`
- `LICENSE`

## Contributing
- Contributions welcome. Open an issue or PR with a clear description and tests where appropriate.
- Please avoid committing credentials or sensitive data.

## License
- See `LICENSE` for license information.

---