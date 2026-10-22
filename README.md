# 🤖 MultiFeature ChatBOT — LangGraph Agentic AI

A **production-ready, multi-feature AI chatbot** built with **LangGraph**, **Groq LLMs**, **Tavily Search**, and a **Streamlit** frontend. The application bundles three distinct AI-powered use cases under a single configurable interface, all orchestrated by LangGraph's stateful graph architecture.

---

## ✨ Features

### 1. 💬 Basic Chatbot
A clean, conversational AI chatbot powered by Groq's LLM (LLaMA / GPT-based models). Handles multi-turn dialogue with stateful message history managed via LangGraph's `StateGraph`.

### 2. 🌐 Chatbot with Web Search
An **agentic chatbot** that dynamically decides when to perform a real-time web search using **Tavily Search API**. Powered by LangGraph's `tools_condition` and `ToolNode` for seamless tool-calling logic:
- LLM reasons about whether to call the search tool
- Web search results are injected back into the conversation
- Final answer is synthesized with up-to-date information

### 3. 📰 AI News Summarizer
An **automated AI news pipeline** that fetches, summarizes, and saves the latest AI news from around the world (with a focus on India):
- Fetches top AI news via Tavily (up to 15 articles)
- Supports **Daily / Weekly / Monthly** timeframes
- Summarizes articles in structured **Markdown** using an LLM
- Saves reports to `./AINews/<timeframe>_summary.md`
- Displays formatted news directly in the Streamlit UI

---

## 🏗️ Architecture

The project follows a clean, modular architecture built around **LangGraph's StateGraph** pattern:

```
app.py
└── src/langgraphagenticai/
    ├── main.py                  # App entry point & orchestration
    ├── LLMS/
    │   └── groqllm.py           # Groq LLM configuration & initialization
    ├── graph/
    │   └── graph_builder.py     # LangGraph graph definitions per use case
    ├── nodes/
    │   ├── basic_chatbot_node.py        # Basic chatbot node logic
    │   ├── chatbot_with_Tool_node.py    # Tool-enhanced chatbot node
    │   └── ai_news_node.py              # Fetch → Summarize → Save pipeline
    ├── tools/
    │   └── search_tool.py       # Tavily Search tool & ToolNode factory
    ├── state/
    │   └── state.py             # LangGraph State schema (TypedDict)
    └── ui/
        ├── uiconfigfile.ini     # App configuration (models, use cases, titles)
        ├── uiconfigfile.py      # Config reader
        └── streamlitui/
            ├── loadui.py        # Sidebar UI loader & user input handler
            └── display_result.py  # Result rendering per use case
```

### LangGraph Flow Diagrams

**Basic Chatbot**
```
START → [chatbot node] → END
```

**Chatbot with Web Search**
```
START → [chatbot node] ──(tool call?)──→ [tool node (Tavily)] → [chatbot node] → END
                        └──(no tool)──→ END
```

**AI News Summarizer**
```
START → [fetch_news] → [summarize_news] → [save_result] → END
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **LLM Orchestration** | [LangGraph](https://github.com/langchain-ai/langgraph) |
| **LLM Provider** | [Groq](https://groq.com/) (LLaMA, GPT-OSS models) |
| **Web Search** | [Tavily Search API](https://tavily.com/) |
| **LLM Framework** | [LangChain](https://python.langchain.com/) (`langchain`, `langchain_core`, `langchain_community`, `langchain_groq`) |
| **Frontend** | [Streamlit](https://streamlit.io/) |
| **Vector Search** | [FAISS](https://faiss.ai/) (`faiss-cpu`) |
| **Language** | Python 3.10+ |

---

## 📋 Prerequisites

- Python **3.10** or higher
- A **[Groq API Key](https://console.groq.com/keys)** (free tier available)
- A **[Tavily API Key](https://app.tavily.com/home)** (required for Web Search & AI News features)

---

## 🚀 Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/MultiFeature_ChatBOT.git
cd MultiFeature_ChatBOT
```

### 2. Create & Activate a Virtual Environment

```bash
# Create virtual environment
python -m venv .venv

# Activate — Windows
.venv\Scripts\activate

# Activate — macOS/Linux
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the Application

```bash
streamlit run app.py
```

The app will open at **`http://localhost:8501`** in your browser.

---

## 🔑 API Key Configuration

API keys are entered securely via the **Streamlit sidebar** at runtime — no `.env` file is required:

| Key | Required For | Where to Get |
|---|---|---|
| `GROQ_API_KEY` | All features | [console.groq.com/keys](https://console.groq.com/keys) |
| `TAVILY_API_KEY` | Chatbot with WebSearch, AI News | [app.tavily.com/home](https://app.tavily.com/home) |

> **Note:** API keys are stored only in `st.session_state` for the duration of your session and are never persisted to disk.

---

## 🖥️ Usage

1. **Launch** the app with `streamlit run app.py`
2. In the **sidebar**:
   - Select your **LLM provider** (currently: Groq)
   - Choose a **Groq model** from the dropdown
   - Enter your **GROQ API Key**
   - Select a **Use Case** from the dropdown
3. If using **Chatbot with WebSearch** or **AI News**, also enter your **Tavily API Key**
4. For **AI News**, select a timeframe (Daily / Weekly / Monthly) and click **"Fetch latest AI News"**
5. For chatbot modes, type your message in the chat input and press Enter

---

## ⚙️ Configuration

App-level settings are managed via [`src/langgraphagenticai/ui/uiconfigfile.ini`](src/langgraphagenticai/ui/uiconfigfile.ini):

```ini
[DEFAULT]
PAGE_TITLE = LangGraph: Build Stateful Agentic AI graph
LLM_OPTIONS = Groq
USECASE_OPTIONS = Basic Chatbot, Chatbot with WebSearch, AI News
GROQ_MODEL_OPTIONS = meta-llama/llama-prompt-guard-2-22m, openai/gpt-oss-120b
```

To add new models or use cases, simply update this file — no code changes needed.

---

## 📁 Output Files

The **AI News Summarizer** saves formatted Markdown reports to the `AINews/` directory:

```
AINews/
├── daily_summary.md     # Generated for "Daily" timeframe
├── weekly_summary.md    # Generated for "Weekly" timeframe
└── monthly_summary.md   # Generated for "Monthly" timeframe
```

Reports follow this structure:
```markdown
# Daily AI News Summary

### YYYY-MM-DD
- [Summary sentence](source-url)
- [Summary sentence](source-url)
```

---

## 📦 Dependencies

```text
langchain
langgraph
langchain_community
langchain_core
langchain_groq
faiss-cpu
streamlit
tavily-python
```

Install all with:
```bash
pip install -r requirements.txt
```

---

## 🗂️ Project Structure

```
MultiFeature_ChatBOT/
├── app.py                          # Application entry point
├── requirements.txt                # Python dependencies
├── .gitignore
├── AINews/                         # Generated news summaries (output)
│   └── daily_summary.md
└── src/
    └── langgraphagenticai/
        ├── __init__.py
        ├── main.py                 # Core app loader & orchestration
        ├── LLMS/
        │   ├── __init__.py
        │   └── groqllm.py          # Groq ChatGroq LLM wrapper
        ├── graph/
        │   ├── __init__.py
        │   └── graph_builder.py    # LangGraph StateGraph definitions
        ├── nodes/
        │   ├── basic_chatbot_node.py
        │   ├── chatbot_with_Tool_node.py
        │   └── ai_news_node.py
        ├── tools/
        │   ├── __init__.py
        │   └── search_tool.py      # Tavily Search tool integration
        ├── state/
        │   └── state.py            # LangGraph State TypedDict
        └── ui/
            ├── __init__.py
            ├── uiconfigfile.ini    # App settings config file
            ├── uiconfigfile.py     # Config reader class
            └── streamlitui/
                ├── loadui.py       # Sidebar & UI loader
                └── display_result.py  # Result display per use case
```

---

## 🤝 Contributing

Contributions are welcome! To contribute:

1. **Fork** the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -m "feat: add your feature"`
4. Push to your branch: `git push origin feature/your-feature-name`
5. Open a **Pull Request**

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).

---

## 🙏 Acknowledgements

- [LangChain](https://python.langchain.com/) & [LangGraph](https://github.com/langchain-ai/langgraph) for the agentic AI framework
- [Groq](https://groq.com/) for blazing-fast LLM inference
- [Tavily](https://tavily.com/) for real-time web search API
- [Streamlit](https://streamlit.io/) for the rapid UI development framework