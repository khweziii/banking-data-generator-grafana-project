# Banking Data Generator (Grafana Project)

This project generates synthetic banking transaction data and inserts it into a PostgreSQL database for exploration and visualization via Grafana.

**Key points**
- Generates fake transactions using `Faker`, `pandas`, and `random`.
- Applies simple rule logic to mark transactions and decisions (approved/rejected).
- Inserts records into a PostgreSQL table `banking_data` using `psycopg2`.

**Prerequisites**
- Python 3.8+
- PostgreSQL (local or container)
- `docker-compose` (optional; a `docker-compose.yml` is included in the repository)

**Install & setup**
- Create and activate a virtual environment:

  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  ```

- Install Python dependencies:

  ```bash
  pip install -r requirements.txt
  ```

**Environment variables**
The app reads DB connection values from environment variables. Create a `.env` file in the repository root with the following values:

```
HOST=**host-name**
PORT=**port-number5432**
DBNAME=**db-name**
USER=**user**
PASSWORD=**password**
```

In this project a PostgreSQL instance was created in AWS and then connected to DBeaver so the data could be explored and queried from a GUI client.

If you prefer to run PostgreSQL locally via Docker Compose (the repo includes `docker-compose.yml`), you can start it with:

```bash
docker-compose up -d
```

**Database table**
The notebook/script will create the `banking_data` table if it doesn't exist. Schema used:

```
CREATE TABLE IF NOT EXISTS banking_data (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMPTZ NOT NULL,
    uniq_id UUID NOT NULL,
    trans_type VARCHAR(50) NOT NULL,
    amount DECIMAL(10, 2) NOT NULL,
    amount_crr DECIMAL(10, 2) NOT NULL,
    account_holder_name VARCHAR(100) NOT NULL,
    card_presence VARCHAR(50) NOT NULL,
    merchant_category VARCHAR(50) NOT NULL,
    card_type VARCHAR(50) NOT NULL,
    card_id VARCHAR(20) NOT NULL,
    account_id UUID NOT NULL,
    account_blacklisted BOOLEAN NOT NULL,
    rules_triggered VARCHAR(100),
    rules_explanation VARCHAR(100),
    decision VARCHAR(100)
);
```

**How to run**
- Open and run the notebook: [app.ipynb](app.ipynb#L1-L999)

- Or convert the notebook to a script and run it:

  ```bash
  jupyter nbconvert --to script app.ipynb --output app
  python app.py
  ```

The generator runs in an infinite loop by default, producing `num_records` per loop and sleeping for 15 seconds. Modify `num_records` or the main loop in the notebook/script as needed.

**Notes**
- The `run_rules` function applies simple business rules and returns a single-row record dict that is inserted into Postgres.
- Use `requirements.txt` to inspect exact dependency versions.

**Next steps / ideas**
- Add CLI flags to control number of records and run duration.
- Add a lightweight producer that writes to Kafka for streaming demos.
- Add tests for the rules logic.

**License**
This project has no license file. Add one if you plan to publish or share the code.
