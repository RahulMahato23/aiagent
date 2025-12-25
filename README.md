# AI Agent 🔧🤖

**AI Agent** is a small example project that demonstrates a simple AI-driven coding agent workflow using Google Gemini (via the `google-genai` package). The agent is able to:

- List files and directories in a constrained working directory
- Read file contents (with a safe truncation limit)
- Execute Python files and capture stdout/stderr
- Write or overwrite files inside the working directory

---

## 🔍 Project structure

Key files and folders:

- `main.py` — Agent entry point that uses the Gemini `generate_content` API and exposes tools to the model.
- `call_function.py` — Maps function call requests from the agent to local helper functions.
- `functions/` — Utility functions exposed to the agent:
  - `get_files_info.py` — List directory contents and sizes
  - `get_file_content.py` — Read file content (truncated at `MAX_CHARS`)
  - `run_python_file.py` — Execute a Python file and return stdout/stderr
  - `write_file.py` — Write files safely within the working directory
- `calculator/` — A small sample project (calculator) that the agent operates on:
  - `main.py` — Simple CLI calculator
  - `pkg/calculator.py` — Expression evaluator logic
  - `pkg/render.py` — JSON formatter
  - `tests.py` — Unit tests for the calculator
- `pyproject.toml` — Project metadata and dependencies

---

## ⚠️ Requirements

- Python 3.12+
- `google-genai` (referenced in `pyproject.toml`)
- `python-dotenv` (optional, for loading `.env` files)

Install dependencies:

```bash
python -m pip install -r <(python - <<'PY'
import tomllib, sys
print('\n'.join([d.strip('"') for d in tomllib.loads(open('pyproject.toml','rb').read())['project']['dependencies']]))
PY)
```

Or, simply:

```bash
python -m pip install google-genai==1.12.1 python-dotenv==1.1.0
```

---

## ⚙️ Setup

1. Create a `.env` file at the repo root or set the environment variable `GEMINI_API_KEY` with your Google Gemini API key.

```bash
# Example: on Windows PowerShell
$env:GEMINI_API_KEY = "your_api_key_here"
```

2. The agent assumes a default working directory of `calculator` (see `call_function.py`). Functions are constrained to operate inside that directory for safety.

---

## ▶️ Running the agent

Run the agent with a question prompt:

```bash
python main.py "Please inspect the project and run its tests" --verbose
```

- The agent will plan its actions and may call the available tools to inspect files, run tests, or modify files as required.
- The `--verbose` flag prints additional token and function call info used by the agent.

---

## 🧪 Running tests and examples

Calculator unit tests:

```bash
python -m unittest calculator.tests
```

Example scripts to exercise the tool functions (manual scripts in the project root):

```bash
python test_get_file_content.py
python test_get_files_info.py
python test_run_python_file.py
python test_write_file.py
```

---

## 🧩 How it works (brief)

- `main.py` creates a `genai.Client` and gives the model a system prompt that describes available tools.
- The model returns candidates and may request tool calls; those tool calls are routed through `call_function.py` to python functions under `functions/`.
- For safety, all functions validate that file paths remain inside the configured working directory.

---

## ✍️ Contributing & License

Contributions are welcome. Please add a `LICENSE` file and update `pyproject.toml` as needed. If you want to propose changes, open an issue or a pull request describing the change.

---

If you want, I can also add example prompts, CI config, or a contributing guideline. ✅
