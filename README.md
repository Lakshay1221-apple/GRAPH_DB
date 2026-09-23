# Graph RAG

Python environment and dependencies for Neo4j graph RAG experiments.

## Setup

Install `uv`, copy `.env.example` to `.env`, and fill in the Neo4j and Groq values:

```bash
cp .env.example .env
uv sync
```

`uv sync` creates or updates the local `.venv` from `pyproject.toml` and `uv.lock`.

Run Python commands in the managed environment with:

```bash
uv run python your_script.py
```

To activate it for an interactive shell instead:

```bash
source .venv/bin/activate
```

Installed integrations include LangChain, LangChain Community, LangChain Groq,
LangChain Neo4j, the Neo4j Python driver, and `python-dotenv`.

## Dependency management

```bash
uv add package-name       # add or upgrade a dependency
uv remove package-name    # remove a dependency
uv lock --upgrade         # refresh locked versions
uv sync                   # apply the lockfile to .venv
```

Keep `.env` and exported Neo4j credential files local. If the credentials in the
existing local export have been shared or committed anywhere, rotate them in Neo4j Aura.