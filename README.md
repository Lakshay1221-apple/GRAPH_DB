# Graph RAG with Neo4j Aura

A Python GraphRAG workspace that extracts entities and relationships with
LangChain, then persists the resulting graph in Neo4j Aura.

The notebook keeps the responsibilities separate:

1. Load configuration from environment variables.
2. Prepare source documents and split them into manageable chunks.
3. Extract LangChain `GraphDocument` objects.
4. Persist the existing graph documents with the official Neo4j driver.

The persistence step does not call the LLM again. It consumes the existing
`graph_documents` variable.

## Requirements

- Python 3.14+
- [`uv`](https://docs.astral.sh/uv/)
- A Neo4j Aura instance
- A Groq API key for graph extraction

## Setup

```bash
cp .env.example .env
uv sync
```

Fill `.env` with your local credentials:

```dotenv
NEO4J_URI=neo4j+s://your-instance.databases.neo4j.io
NEO4J_USERNAME=your-username
NEO4J_PASSWORD=your-password
NEO4J_DATABASE=your-database
GROQ_API_KEY=your-groq-api-key
```

Never commit `.env` or exported Neo4j credential files. Rotate credentials if
they have been exposed.

## Run the notebook

Open `graph_db.ipynb` in VS Code and select the `graph-rag` kernel. Run the
cells from top to bottom:

- Configuration loads `.env` without printing secret values.
- LangChain prepares the source documents.
- `LLMGraphTransformer` creates `graph_documents`.
- The Neo4j persistence cells verify Aura, merge the graph, report progress,
  and close the driver.

The notebook uses `ignore_tool_usage=True` for the configured Groq model so the
transformer uses its JSON parsing fallback instead of relying on unreliable
forced tool calls.

## Neo4j persistence model

- Every extracted entity is stored as a `GraphEntity` node.
- The LangChain node `id` is preserved as the Neo4j `id` property.
- The LangChain node `type` is preserved as `entity_type` and added as a label.
- Relationships are merged by source ID, target ID, and relationship type.
- Each source document is stored as a `SourceDocument` node.
- Extracted entities connect to their source with `EXTRACTED_FROM`.
- Source metadata is stored as JSON in the source node's `metadata` property.

Node and relationship values are passed as Cypher parameters. Dynamic labels
and relationship types are sanitized before being placed in Cypher because
Neo4j does not allow labels or relationship types to be query parameters.

## Run Python commands

```bash
uv run python your_script.py
```

Or activate the environment for an interactive shell:

```bash
source .venv/bin/activate
```

## Dependency management

```bash
uv add package-name       # add a dependency
uv remove package-name    # remove a dependency
uv lock --upgrade         # refresh locked versions
uv sync                   # install the lockfile
```

The project includes LangChain, LangChain Community, LangChain Groq,
LangChain Neo4j, the Neo4j Python driver, `python-dotenv`, and `ipykernel`.