# ⛈️ Weather & Multimodal AI Agent Showcase: The Versatile ReAct Model

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/ "Requires Python 3.10 or newer")
[![Framework](https://img.shields.io/badge/Framework-LangChain-brightgreen)](https://www.langchain.com/)
[![Agent Type](https://img.shields.io/badge/Agent-ReAct%20Executor-orange)](https://docs.langchain.com/docs/components/agents/agenttypes/react)
[![Model](https://img.shields.io/badge/Model-GPT--4o--mini-informational)](https://openai.com/ "Uses the latest cost-effective GPT model")
[![Open Issues](https://img.shields.io/github/issues/Abdul-Rehman6/Langsmith-Integrations)](https://github.com/Abdul-Rehman6/Langsmith-Integrations/issues)

## 🌟 Project Overview

This repository features a powerful Jupyter Notebook (`Image_Weather_Ai_Agent.ipynb`) that demonstrates a sophisticated **LangChain ReAct Agent** capable of complex decision-making by leveraging three distinct types of tools:

1.  **Multimodal Tool (Vision):** Analyzing weather conditions directly from an image URL using GPT-4o-mini.
2.  **API Tool:** Fetching real-time, structured weather data from a dedicated API.
3.  **Search Tool:** Performing general web searches for information outside of structured APIs.

The Agent utilizes the **ReAct (Reasoning and Acting)** prompt to dynamically select the right tool, execute a sequence of actions, and synthesize the final answer. This showcase highlights how to build agents that seamlessly transition between knowledge domains and input modalities.

---

## ✨ Agent Capabilities & Tools

The core of this Agent is its ability to choose the most appropriate tool for the task, showcasing a practical application of tool-use in LLM development.

| Tool Name | Type | Purpose & Integration |
| :--- | :--- | :--- |
| `analyze_weather_from_image` | **Custom Multimodal (GPT-4o-mini)** | Analyzes a given `image_url` of a sky or scene to **describe the visual weather conditions** (e.g., clear, cloudy, rainy) and image content. |
| `get_weather_data` | **Custom API Wrapper** | Fetches structured, **real-time weather data** (temperature, humidity, wind speed, etc.) for a specific `location` using the Weatherstack API. |
| `tavily_search` | **Pre-built Web Search** | Provides **general knowledge and current information** (e.g., population data, historical facts) essential for handling general queries outside of the weather domain. |

---

## 🚀 Getting Started

Follow these steps to set up your environment and run the notebook.

### Prerequisites

You must have an account and API keys for the following services:

* **Python 3.10+**
* **OpenAI API Key** (Required for the LLM core and the Multimodal tool).
* **Tavily API Key** (Required for the `tavily_search` tool).
* **Weatherstack API Key** (Required for the `get_weather_data` tool).

### Installation Steps

1.  **Clone the Repository:**

    ```bash
    git clone [YOUR_REPO_URL] 
    cd Langsmith-Integrations
    ```

2.  **Install Dependencies:**

    Install the necessary packages, including `langchain-openai`, `langchain-tavily`, and `python-dotenv`.

    ```bash
    pip install -r requirements.txt
    ```

3.  **Set Up Environment Variables:**

    Create a file named **`.env`** in your project root and populate it with your API keys:

    ```bash
    # OpenAI API Key for LLM and Vision Model (GPT-4o-mini)
    OPENAI_API_KEY="YOUR_OPENAI_API_KEY"

    # Tavily Search API Key
    TAVILY_API_KEY="YOUR_TAVILY_API_KEY"

    # NOTE: The Weatherstack key is currently hardcoded in the notebook for demonstration. 
    # For a production application, you should move this key to the .env file as well.
    # WEATHERSTACK_API_KEY="YOUR_WEATHERSTACK_API_KEY" 
    ```

---

## 💻 Running the Agent (Code Highlights)

The notebook executes a series of steps to instantiate and run the fully capable Agent.

### 1. Custom Tools Implementation

Two custom tools are defined using the `@tool` decorator:

* **`analyze_weather_from_image`**: This tool uses `gpt-4o-mini` to handle **multimodal input**, passing a list of content parts (`text` prompt and `image_url`).
* **`get_weather_data`**: A standard API wrapper that fetches structured JSON data from Weatherstack.

### 2. Agent Initialization

The **ReAct Agent** is constructed using the `create_react_agent` utility with the prompt pulled from the LangChain Hub (`hwchase17/react`), ensuring a robust reasoning structure.

```python
# Load Prompt Template
prompt = hub.pull("hwchase17/react")

# Create the Agent (LLM, all three tools, and the ReAct prompt)
agent = create_react_agent(
    llm=ChatOpenAI(model="gpt-4o-mini"),
    tools=[search_tool, get_weather_data, analyze_weather_from_image],
    prompt=prompt
)

# Create the Executor with verbose output enabled
agent_executer = AgentExecutor(
    agent=agent,
    tools=[search_tool, get_weather_data, analyze_weather_from_image],
    verbose=True
)
