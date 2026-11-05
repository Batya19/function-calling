# Function Calling – Task Management Agent

A small task-management assistant built with **OpenAI function calling**.
The user writes a request in natural language (Hebrew or English), the model decides
which function to call, the function runs locally, and the model phrases the final answer.

## How it works

1. `main.py` – FastAPI app exposing `POST /chat` with a JSON body `{"message": "..."}`.
2. `agent_service.py` – sends the message to the model together with four tool definitions
   (`add_task`, `get_tasks`, `update_task`, `delete_task`), executes the tool the model picked,
   feeds the result back, and returns the model's final reply.
3. `todo_service.py` – in-memory task store. Each task has an id, title, description, type,
   start/end date and status.

## Setup

Requires Python 3.8+ and [uv](https://docs.astral.sh/uv/).

```bash
uv sync
```

Create a `.env` file:

```
OPENAI_API_KEY=your_key_here
```

## Run

Start the API:

```bash
uv run uvicorn main:app --reload
```

Then send a request:

```bash
curl -X POST http://127.0.0.1:8000/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "תוסיף משימה לקנות חלב"}'
```

## Tests / examples

- `test.py` – exercises the task store directly, without the model.
- `test_agent.py` – runs a full add / list / update / delete flow through the agent.

```bash
uv run python test.py
uv run python test_agent.py
```

## Notes

- Tasks live in memory only and are reset when the server restarts.
- Default task type is `כללי` and default status is `חדש`.
