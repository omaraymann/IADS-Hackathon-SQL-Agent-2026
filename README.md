# IADS Hackathon SQL Agent 2026

An agentic text-to-SQL assistant built for the IADS Agentic AI Hackathon 2026.
The project combines retrieval-augmented context, Oracle Select AI, SQL
validation, and safety guardrails so users can ask business questions in plain
English and inspect the SQL behind the answer.

## What This Project Does

The agent turns a natural-language question such as:

```text
What were total sales by product category?
```

into a structured SQL-generation workflow:

1. Retrieve relevant schema and business context.
2. Build a grounded SQL-generation prompt.
3. Generate SQL with Oracle Select AI.
4. Validate the generated query before execution.
5. Return the SQL, supporting context, and response metadata.

The repository includes a sample sales dataset, prompt templates, tests,
evaluation data, deployment scaffolding, and documentation for the intended
multi-agent architecture.

## Key Features

- Retrieval-augmented SQL prompting over schema and domain context.
- Oracle Select AI integration through `DBMS_CLOUD_AI.GENERATE`.
- SQL validation and guardrails for safer query generation.
- Modular agent pipeline with planner, retriever, generator, critic, and
  summariser components.
- Streamlit frontend scaffold and FastAPI backend structure.
- Benchmark and evaluation datasets for demo and regression testing.
- Docker, Makefile, and documentation for reproducible development.

## Repository Structure

```text
app/                 Lightweight runnable RAG-to-SQL demo pipeline
src/sql_agent/       Main package structure for the production-style agent
frontend/            Streamlit UI entry point
prompts/             Versioned prompts for planning, SQL, critique, and summary
db/                  DDL, glossary, and schema descriptions
data/                Raw and placeholder datasets
evaluation/          Benchmark harness and demo query datasets
tests/               Unit and integration tests
docs/                Architecture notes, lifecycle docs, and ADRs
scripts/             Data prep, schema embedding, seeding, and benchmark scripts
deploy/              Deployment notes
```

## Architecture

```text
User question
    |
    v
Planner
    |
    v
Schema / context retriever
    |
    v
SQL prompt builder
    |
    v
Oracle Select AI SQL generator
    |
    v
SQL validator and safety checks
    |
    v
Safe execution / response generation
```

For more detail, see:

- [Architecture](docs/ARCHITECTURE.md)
- [Request lifecycle](docs/REQUEST_LIFECYCLE.md)
- [Evaluation plan](docs/EVALUATION.md)
- [Architecture decision records](docs/decisions)

## Requirements

- Python 3.11+
- Oracle Autonomous Database access for live SQL generation
- Oracle Select AI profile configured in the database
- OCI configuration for GenAI and embedding features where applicable

## Quick Start

Clone the repository:

```bash
git clone https://github.com/omaraymann/IADS-Hackathon-SQL-Agent-2026.git
cd IADS-Hackathon-SQL-Agent-2026
```

Create and activate a virtual environment:

```bash
python -m venv .venv
.venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
pip install -r requirements-dev.txt
pip install -e .
```

Create your environment file:

```bash
copy .env.example .env
```

Then edit `.env` with your OCI, Autonomous Database, wallet, and Select AI
settings.

## Running the Demo Pipeline

Run the lightweight demo pipeline:

```bash
python -m app.main "What were total sales by product category?"
```

This prints:

- The original question.
- Retrieved context documents.
- The SQL-generation prompt.
- Generated SQL output or a clear skip/error reason.
- The current pipeline stage.

## Running the API and UI

Start the FastAPI service:

```bash
make run-api
```

Start the Streamlit interface:

```bash
make run-ui
```

The Makefile also includes useful development commands:

```bash
make test
make lint
make format
make benchmark
```

## Configuration

The most important environment variables are:

```text
SELECT_AI_PROFILE
ADB_USER
ADB_PASSWORD
ADB_DSN
ADB_WALLET_LOCATION
OCI_COMPARTMENT_ID
OCI_REGION
OCI_GENAI_MODEL_ID
OCI_EMBED_MODEL_ID
```

Use `.env.example` as the source of truth for all supported settings.

## Testing

Run the test suite with:

```bash
pytest
```

The current tests cover SQL generation, validation, prompt construction, safe
execution behavior, and the end-to-end agent pipeline.

## Dataset

The repository includes a product sales dataset under:

```text
data/raw/product_sales_dataset_final.csv
```

This is used for exploration, demo queries, and evaluation artifacts.

## Security Notes

- Keep real credentials in `.env`; do not commit secrets.
- Use a read-only database user for generated SQL execution.
- Validate generated SQL before running it against live data.
- Keep row limits and query timeouts enabled for demo and production use.

See [SECURITY.md](SECURITY.md) for additional guidance.

## License

MIT. See [LICENSE](LICENSE).
