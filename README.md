# Drug Interaction Agent

An Agentic GraphRAG project for drug interaction analysis using:
- Neo4j knowledge graphs
- LLM-powered reasoning and retrieval
- Structured medical/drug interaction data
- Jupyter notebooks for ETL and agent workflows

This repository demonstrates a practical end-to-end workflow:
1. Build and seed a medical knowledge graph.
2. Run graph-aware retrieval and reasoning.
3. Answer drug interaction questions with grounded graph context.

## Features

- Graph schema and ingestion pipeline for drugs, conditions, side effects, and interactions.
- Config-driven model/provider setup with YAML + environment variables.
- Support for OpenRouter (recommended) and direct providers.
- Utility modules for Neo4j connectivity, model services, and prompt templates.
- Notebook-first workflow for rapid experimentation.

## Project Structure

```text
drug-interaction-agent/
├── config/
│   ├── models.yaml                 # Model names by provider/tier
│   └── params.yaml                 # Runtime settings (provider, retrieval, paths)
├── data/
│   └── raw/
│       └── drug_interactions.json  # Curated structured dataset
├── notebooks/
│   ├── 01_knowledge_graphs_and_etl.ipynb
│   └── 02_agentic_graph_rag.ipynb
├── scripts/
│   ├── init_graph.py               # Create constraints/indexes (optional clean)
│   └── seed_graph.py               # Load dataset into Neo4j
├── utils/
│   ├── config.py                   # Config loading/validation helpers
│   ├── llm_services.py             # LLM/embedding factories
│   ├── neo4j_client.py             # Neo4j client wrapper
│   └── prompts.py                  # Prompt templates
├── .env.example
├── requirements.txt
└── README.md
```

## Data Model

### Node Labels

- `Drug`
- `Condition`
- `SideEffect`
- `DrugClass`

### Relationship Types

- `INTERACTS_WITH` (Drug -> Drug)
- `TREATS` (Drug -> Condition)
- `CAUSES_SIDE_EFFECT` (Drug -> SideEffect)
- `CONTRAINDICATED_FOR` (Drug -> Condition)
- `BELONGS_TO_CLASS` (Drug -> DrugClass)

### Interaction Properties

`INTERACTS_WITH` relationships can include:
- `severity`
- `mechanism`
- `effect`
- `recommendation`
- `source`

## Requirements

- Python 3.10+ (3.11 recommended)
- Neo4j database (Neo4j Aura Free works well)
- API key for your selected LLM provider

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/p-sahas/drug-interaction-agent.git
cd drug-interaction-agent
```

### 2. Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
python -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Copy and edit the environment file:

```bash
cp .env.example .env
```

On Windows PowerShell, if `cp` is unavailable:

```powershell
Copy-Item .env.example .env
```

Set required values in `.env`:

- `NEO4J_URI`
- `NEO4J_USERNAME`
- `NEO4J_PASSWORD`
- `OPENROUTER_API_KEY` (if using OpenRouter)
- `OPENAI_API_KEY` (if using direct OpenAI and configured in `config/params.yaml`)

### 5. Configure runtime settings (optional)

Edit:
- `config/params.yaml` for provider, model tier, retrieval, and pipeline settings.
- `config/models.yaml` for model IDs by provider/tier.

Default provider is OpenRouter.

## Initialize and Seed the Graph

### Initialize schema

Creates constraints and indexes:

```bash
python scripts/init_graph.py
```

Optional clean + schema reset:

```bash
python scripts/init_graph.py --clean
```

### Seed with dataset

Load `data/raw/drug_interactions.json` into Neo4j:

```bash
python scripts/seed_graph.py
```

Optional clean before seed:

```bash
python scripts/seed_graph.py --clean-first
```

## Run Notebooks

Start Jupyter:

```bash
jupyter notebook
```

Then run notebooks in order:
1. `notebooks/01_knowledge_graphs_and_etl.ipynb`
2. `notebooks/02_agentic_graph_rag.ipynb`

## Configuration Overview

### `config/params.yaml`

Controls:
- Provider and model tier selection
- LLM defaults (temperature, max tokens)
- Embedding tier and batch settings
- Neo4j environment variable mapping
- ETL extraction chunking and limits
- Retrieval/agent parameters
- Path and logging behavior

### `config/models.yaml`

Defines model IDs for:
- OpenRouter
- OpenAI
- Anthropic
- Google
- Groq
- DeepSeek

## Troubleshooting

- Neo4j connection errors:
  - Verify `NEO4J_URI`, `NEO4J_USERNAME`, and `NEO4J_PASSWORD` in `.env`.
  - Ensure your Aura instance is running and accessible.

- Missing API key errors:
  - Confirm provider in `config/params.yaml`.
  - Add corresponding API key to `.env`.

- Module import issues:
  - Activate your virtual environment.
  - Reinstall dependencies with `pip install -r requirements.txt`.

- Notebook kernel issues:
  - Install ipykernel in your venv and select that kernel in Jupyter/VS Code.

## Security and Privacy Notes

- Do not commit `.env` or secrets.
- Use minimum required permissions for external services.
- Review generated/LLM outputs before operational or clinical usage.

## Medical Disclaimer

This project is for educational and research purposes only.
It is not medical advice and must not replace professional clinical judgment.
Always consult qualified healthcare professionals for diagnosis and treatment decisions.

## Contributing

Contributions are welcome. Please:
1. Open an issue describing the change.
2. Keep changes focused and well-documented.
3. Include reproducible steps for bug fixes.

## License

This project is licensed under the MIT License. See the `LICENSE` file for details.
