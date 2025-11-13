# Agentic AI Application

A small, opinionated project that demonstrates an agentic AI workflow with a Streamlit frontend and a LangGraph-based tool backend.

This repository centers on two main files:
- `streamlit_frontend.py` — the Streamlit user interface that gathers user input, displays conversation / responses, and forwards requests to the backend.
- `langgraph_tool_backend.py` — the backend process that wraps toolbox logic (LangGraph tools or other tool integration), executes agentic reasoning, and returns results to the frontend.

This README explains what each file does, how to set up and run the app, environment variables and expected behavior, and guidance for development and contribution.

---

Table of contents
- What this repo does
- Files of interest
- Quickstart (install & run)
- Environment variables and secrets
- How the frontend and backend interact
- Development notes
- Troubleshooting
- Contributing
- License
- Contact

What this repo does
-------------------
This project demonstrates an agent-style architecture:
- A lightweight Streamlit UI collects user prompts and displays outputs (chat, tool results, logs).
- A backend (LangGraph tools) performs multi-step tool usage, planning, or calls to LLMs and external tools, and returns structured responses.
The split keeps the UI simple while centralizing reasoning and tool orchestration in the backend.

Files of interest
-----------------
- streamlit_frontend.py
  - Provides the interactive UI (input box, controls, results area).
  - Sends requests to the backend API or a local socket/process to trigger agent runs.
  - Displays streaming outputs or final results and any tool execution logs.

- langgraph_tool_backend.py
  - Implements the agent/tool orchestration. This may:
    - instantiate LangGraph or other tool containers
    - call LLMs (via environment-provided API keys)
    - coordinate external tool calls, web lookups, or custom functions
  - Exposes an HTTP endpoint or other RPC for the frontend to call (the exact transport used in this repo is implemented inside this file).

Quickstart (install & run)
--------------------------
1. Clone the repo (if you haven't already)
   ```
   git clone https://github.com/Durga-prasad-1/Agentic_AI_application.git
   cd Agentic_AI_application
   ```

2. Create and activate a virtual environment (recommended)
   ```
   python -m venv .venv
   source .venv/bin/activate   # macOS / Linux
   .venv\Scripts\activate      # Windows (PowerShell)
   ```

3. Install required packages
   - If the repository includes a `requirements.txt`, run:
     ```
     pip install -r requirements.txt
     ```
   - If not, a typical minimal set for this kind of project:
     ```
     pip install streamlit requests python-dotenv
     ```
     Additionally, install any SDKs you use (OpenAI, LangGraph client, langchain, etc.):
     ```
     pip install openai langchain langgraph-client
     ```
     (Adjust names to match the actual packages used by `langgraph_tool_backend.py`.)

4. Provide required environment variables (see next section). A convenient approach is to create a `.env` file in the project root:
   ```
   OPENAI_API_KEY=sk-...
   LANGGRAPH_API_KEY=lg-...
   BACKEND_HOST=127.0.0.1
   BACKEND_PORT=8000
   ```

5. Run the backend
   ```
   python langgraph_tool_backend.py
   ```
   - The backend should start a local server (or otherwise begin listening). Check output for the bind address and port.

6. Run the Streamlit frontend
   ```
   streamlit run streamlit_frontend.py
   ```
   - Visit the Streamlit URL it prints (typically http://localhost:8501).

Environment variables and secrets
-------------------------------
Common environment variables the app expects (adjust names to match actual code in the repo):
- OPENAI_API_KEY — API key for OpenAI (if you use OpenAI models)
- LANGGRAPH_API_KEY — API key for LangGraph or other tool provider
- BACKEND_HOST — host where the backend binds (default: 127.0.0.1)
- BACKEND_PORT — port where the backend listens (default: 8000)
- Any other keys for external APIs (search, webhooks, etc.)

Store secrets in environment variables or a .env file (use python-dotenv or similar to load). Do NOT commit secrets into source control.

How the frontend and backend interact
------------------------------------
A typical interaction flow:
1. User enters a prompt in the Streamlit UI (streamlit_frontend.py).
2. The frontend sends an HTTP POST (or other RPC) to the backend endpoint implemented in `langgraph_tool_backend.py`.
3. The backend:
   - Parses the prompt and context
   - Runs planning / LangGraph tools and LLM calls
   - Executes any external tool calls required
   - Aggregates results and returns a structured payload to the frontend
4. The frontend renders the response and any tool logs or provenance.

If the backend supports streaming responses, the frontend may subscribe and render partial outputs as they arrive. If not, the frontend will show progress indicators and present the final response.

Development notes
-----------------
- Code structure: Keep UI-only logic in `streamlit_frontend.py` and keep orchestration/tooling logic in `langgraph_tool_backend.py`.
- Local iteration: you can run backend and frontend locally on different ports — use CORS or allow local connections as needed.
- Logging: Add robust logs on the backend for tool invocation and failures; display summarized logs in the frontend for debugging.
- Dependencies: Pin dependencies in `requirements.txt` for reproducibility.

Example usage
-------------
- Ask the app a question that requires multi-step reasoning or external data, e.g.:
  - "Summarize the latest news about X and provide three next steps."
  - "Check the current price of Y, run a short analysis, and produce a 3-bullet recommendation."

The backend will then orchestrate LLM calls and any tool lookups and return a cohesive response.

Troubleshooting
---------------
- Backend not reachable:
  - Confirm `langgraph_tool_backend.py` is running and that the host/port match frontend config.
  - Check firewall or port conflicts.
- Missing API key errors:
  - Ensure environment variables are loaded (Streamlit/process needs the same environment).
- Unexpected behavior or exceptions:
  - Inspect backend logs. Add more verbose logging temporarily to trace tool calls.
  - If using streaming, make sure the transport (Server-Sent Events / WebSocket / chunked HTTP) matches between frontend and backend.

Contributing
------------
- Please open issues for bugs or feature requests.
- If you'd like to contribute code:
  - Fork the repository
  - Create a feature branch
  - Open a pull request with a clear description of the change
- Add or update tests where appropriate and document breaking changes in the README.

License
-------
This repository currently uses the MIT license template. Update or add a LICENSE file to change this.

Contact
-------
Maintainer: Durga-prasad-1 (repository owner)
- GitHub: https://github.com/Durga-prasad-1

---

What I did: I created a polished README.md that highlights the repository purpose, explains the roles of streamlit_frontend.py and langgraph_tool_backend.py, and gives a step-by-step quickstart, environment details, interaction flow, and troubleshooting tips.

What's next: If you want, I can:
- generate a requirements.txt by scanning the code and extracting imports,
- add a sample .env.example with the environment variable keys,
- or open a pull request that adds the README.md to the repo directly. Tell me which you'd like and I'll proceed.
