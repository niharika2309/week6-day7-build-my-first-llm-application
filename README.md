# Build Your First LLM Application

**Week 6, Day 7**: a hands-on notebook that shows what it looks like when *your own Python code* sends a request to an LLM and gets structured data back.

No frameworks, no agents, no vector databases. Just Python, one API call, and the core ideas of the series: **prompting, context, temperature, structured output, and hallucinations**.

## What you'll build

A tiny **document parser**. The notebook takes a short company policy, sends it to an LLM, and extracts four facts as JSON:

| Field | Type | Example |
| --- | --- | --- |
| `policy_name` | `str` | `"Remote Work Policy"` |
| `remote_days` | `int` | `3` |
| `notice_period_days` | `int` | `2` |
| `manager_approval` | `bool` | `True` |

The response is then validated with [Pydantic](https://docs.pydantic.dev/) so your program ends up holding a real, typed object instead of a loose string.

## The pipeline

```
Document  →  Prompt  →  LLM API call  →  Structured response (JSON)  →  Python object
```

This is the same basic pipeline behind every real LLM application. The difference is that here you own every step.

## What you'll learn

- **Prompting**: using a system prompt plus a user prompt to tell the model exactly what to do
- **Context**: the policy document is what the model reasons over
- **Temperature**: setting `temperature=0` for factual, repeatable output
- **Structured output**: describing the JSON shape in the prompt and checking it with Pydantic
- **Hallucinations**: structure guarantees the *shape* of an answer, not its *truth*

## Getting started

### Prerequisites

- Python 3.9+
- Jupyter (JupyterLab, Notebook, VS Code, or Google Colab)
- Access to an OpenAI-compatible LLM endpoint

### Install dependencies

```bash
pip install openai pydantic
```

> Pydantic v2 is required (the notebook uses `model_validate`).

### Configure the LLM connection

The notebook reads your API key from an environment variable and falls back to `"none"` if it isn't set. **Never paste a real key into the notebook.**

```bash
# macOS / Linux
export OPENAI_API_KEY="your-real-key-here"

# Windows (PowerShell)
$env:OPENAI_API_KEY="your-real-key-here"
```

The notebook is set up for the class's local LLM server (model: `Qwen3.8-27B`), which doesn't require a key. The server address is hard-coded in the client setup cell:

```python
client = OpenAI(
    base_url="http://100.117.48.99:8888/v1",
    api_key=os.environ.get("OPENAI_API_KEY", "none"),
)
```

If you're not on the class network, or want to use another provider, change `base_url` (and the `model` name in the API call cell) to match your endpoint, for example `https://api.openai.com/v1` with a real key in `OPENAI_API_KEY`.

### Run it

```bash
jupyter notebook
```

Open the notebook and run the cells from top to bottom.

## Notebook outline

1. **Setup**: API keys and environment variables
2. **Import libraries**: `openai`, `pydantic`, `json`, `os`
3. **Create the sample document**: a short remote work policy
4. **Create the prompt**: system prompt and user prompt
5. **Define the structured output**: a Pydantic `Policy` model
6. **Make the LLM API call**: `client.chat.completions.create(...)`
7. **Print the result**: raw text, then validated object
8. **What just happened?**: the full pipeline, zoomed out
9. **A small experiment**
10. **Structure is not truth**: why validation and evaluation still matter
11. **Try it yourself**

## Experiments to try

1. **Change the document.** Edit the policy to allow 2 remote days, require 5 days' notice, and not require manager approval. Do the extracted values follow?
2. **Add a field.** Ask for something like `approval_process` in the prompt and add it to the `Policy` model.
3. **Ask for something that isn't there.** Add a field like `office_color`. Does the model invent a value or admit the document doesn't say?

## Troubleshooting

- **`json.JSONDecodeError`**: the model replied in plain English or wrapped the JSON in extra text. Reword the prompt or keep `temperature=0`.
- **`ValidationError` from Pydantic**: a field is missing or has the wrong type. This is the check doing its job.
- **Connection errors**: confirm you can reach the `base_url` (you may need to be on the class network or VPN), or point the client at a different provider.

## Key takeaway

> Structured output guarantees the *shape* of the answer, not the *truth* of the answer.

The model is a very confident reader. Your code is the one who double-checks its homework.

## What's next

Making the pipeline robust: better validation, checking answers against the source document, and evaluating your prompts.
