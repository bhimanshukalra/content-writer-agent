# Content Writer Agent

AI-powered technical blog writer that plans, researches, drafts, and assembles Markdown articles using LangGraph and LLM agents.

## What It Does

This project generates technical blog posts from a topic. The workflow can:

- Decide whether a topic needs web research.
- Search and deduplicate evidence for current or source-sensitive topics.
- Create a structured article plan.
- Write sections in parallel.
- Merge sections into a final Markdown article.
- Optionally add image placeholders, generate images, and insert them into the output.
- Stream progress to a FastAPI-powered web UI.

## Architecture

- `server.py` contains the LangGraph workflow.
- `main.py` exposes the workflow through FastAPI and streams progress with Server-Sent Events.
- `templates/` and `static/` contain the browser UI.
- `notebooks/` contains earlier experiments and workflow iterations.
- `outputs/` stores generated Markdown copies by run ID.
- `images/` stores generated images.

## Workflow

```text
router -> optional research -> orchestrator -> parallel workers -> reducer
```

The reducer subgraph handles:

```text
merge_content -> decide_images -> generate_and_place_images
```

## Requirements

- Python 3.12+
- `uv`
- Ollama running locally with `llama3.1:8b`
- PostgreSQL database for LangGraph checkpointing
- API keys for optional research and image generation

## Setup

Install dependencies:

```bash
uv sync
```

Create a `.env` file from the example:

```bash
cp .env.example .env
```

Fill in the required values:

```bash
DATABASE_URL=
TAVILY_API_KEY=
GOOGLE_API_KEY=
```

`DATABASE_URL` is required. `TAVILY_API_KEY` is needed for web research. `GOOGLE_API_KEY` is needed for image generation.

Make sure Ollama is running and the model is available:

```bash
ollama pull llama3.1:8b
ollama serve
```

## Run

Start the FastAPI app:

```bash
uv run uvicorn main:app --reload
```

Then open:

```text
http://127.0.0.1:8000
```

## Notes

- Generated Markdown is saved in `outputs/<run_id>/blog.md`.
- `server.py` may also save a title-based Markdown file in the project root.
- If image generation fails, the app keeps the Markdown usable by inserting a fallback block with the image prompt and error.
