# ⛈️ Weather & Multimodal AI Agent Showcase

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue)](https://www.python.org/ "Requires Python 3.10 or newer")
[![Framework](https://img.shields.io/badge/Framework-LangChain-brightgreen)](https://www.langchain.com/)
[![Agent Type](https://img.shields.io/badge/Agent-ReAct%20Executor-orange)](https://docs.langchain.com/docs/components/agents/agenttypes/react)
[![Model](https://img.shields.io/badge/Model-GPT--4o--mini-informational)](https://openai.com/ "Uses the latest cost-effective GPT model")

This repository contains a Jupyter Notebook (`Image_Weather_Ai_Agent.ipynb`) that demonstrates a sophisticated **LangChain ReAct Agent** capable of complex decision-making by leveraging three distinct types of tools:

1.  **Multimodal Tool (Vision):** Analyzing weather conditions directly from an image URL.
2.  **API Tool:** Fetching real-time, structured weather data from a dedicated API.
3.  **Search Tool:** Performing general web searches for information outside of structured APIs.

The Agent uses the **ReAct (Reasoning and Acting)** prompt to decide which tool to use, the sequence of actions, and how to synthesize the final answer.

---

## ✨ Agent Capabilities & Tools

The core of this Agent is its ability to choose the most appropriate tool for the task, showcasing a practical application of tool-use in LLM development.

| Tool Name | Type | Purpose & Integration |
| :--- | :--- | :--- |
| `analyze_weather_from_image` | **Custom Multimodal (GPT-4o-mini)** | Analyzes a given `image_url` of a sky or scene to **describe the visual weather conditions** (e.g., clear, cloudy, rainy). |
| `get_weather_data` | **Custom API Wrapper** | Fetches structured, **real-time weather data** (temperature, humidity, wind speed, etc.) for a specific `location` using the Weatherstack API. |
| `tavily_search` | **Pre-built Web Search** | Provides **general knowledge and current information** (e.g., population data, historical facts) which is vital for handling queries outside of the weather domain. |

---

## 🚀 Getting Started

Follow these steps to set up your environment and run the notebook.

### Prerequisites

You must have an account and API keys for the following services:

* **Python 3.10+**
* **OpenAI API Key** (Required for the LLM core and the Multimodal tool).
* **Tavily API Key** (Required for the `tavily_search` tool).
* **Weatherstack API Key** (Required for the `get_weather_data` tool - the key in the code is illustrative and should be replaced with your own).

### Installation Steps

1.  **Clone the Repository:**

    ```bash
    git clone [YOUR_REPO_URL] # Replace with your actual repo URL
    cd Langsmith-Integrations
    ```

2.  **Install Dependencies:**

    The code uses `langchain-openai`, `langchain-tavily`, and `python-dotenv`.

    ```bash
    pip install -r requirements.txt # Assuming you have a requirements.txt with the necessary packages
    # If no requirements.txt, install: pip install langchain-openai langchain-tavily python-dotenv requests jupyter
    ```

3.  **Set Up Environment Variables:**

    Create a file named **`.env`** in your project root and populate it with your API keys:

    ```bash
    # OpenAI API Key
    OPENAI_API_KEY="YOUR_OPENAI_API_KEY"

    # Tavily Search API Key
    TAVILY_API_KEY="YOUR_TAVILY_API_KEY"

    # NOTE: The Weatherstack key is currently hardcoded in the notebook. 
    # For better practice, you should update the `get_weather_data` function 
    # to load this key from the .env file as well.
    # WEATHERSTACK_API_KEY="YOUR_WEATHERSTACK_API_KEY" 
    ```

---

## 💻 Running the Agent (Code Highlights)

The notebook executes a series of steps to instantiate the fully capable Agent.

### 1. Custom Tools Implementation

Two custom tools are defined using the `@tool` decorator from `langchain_core.tools`:

* **`analyze_weather_from_image`**: This tool uses `gpt-4o-mini` to handle **multimodal input**, passing a list of content parts (`text` prompt and `image_url`).

    ```python
    @tool
    def analyze_weather_from_image(image_url: str) -> str:
      # ... (Uses ChatOpenAI(model="gpt-4o-mini"))
      # ... (Passes 'image_url' in the 'content' list)
    ```

* **`get_weather_data`**: A standard API wrapper that fetches JSON data.

    ```python
    @tool
    def get_weather_data(location: str) -> str:
        """Searches and returns current weather data for the given location."""
        # Note: API key is hardcoded in the notebook and should be updated.
        url = f"[https://api.weatherstack.com/current?access_key=...&query=](https://api.weatherstack.com/current?access_key=...&query=){location}"
        # ...
    ```

### 2. Agent Initialization

The **ReAct Agent** is constructed using the `create_react_agent` utility with a pre-built prompt from the LangChain Hub (`hwchase17/react`), ensuring best practices for reasoning and action selection.

```python
# Load Prompt Template
prompt = hub.pull("hwchase17/react")

# Create the Agent
agent = create_react_agent(
    llm=ChatOpenAI(model="gpt-4o-mini"),
    tools=[search_tool, get_weather_data, analyze_weather_from_image],
    prompt=prompt
)

# Create the Executor
agent_executer = AgentExecutor(
    agent=agent,
    tools=[search_tool, get_weather_data, analyze_weather_from_image],
    verbose=True
)
