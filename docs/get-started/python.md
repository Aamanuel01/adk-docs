from google.adk.agents import LlmAgent
from google.adk.tools import agent_tool
from google.adk.tools.google_search_tool import GoogleSearchTool
from google.adk.tools import url_context

expense_cutter_google_search_agent = LlmAgent(
  name='Expense_Cutter_google_search_agent',
  model='gemini-3.1-pro-preview',
  description=(
      'Agent specialized in performing Google searches.'
  ),
  sub_agents=[],
  instruction='Use the GoogleSearchTool to find information on the web.',
  tools=[
    GoogleSearchTool()
  ],
)
expense_cutter_url_context_agent = LlmAgent(
  name='Expense_Cutter_url_context_agent',
  model='gemini-3.1-pro-preview',
  description=(
      'Agent specialized in fetching content from URLs.'
  ),
  sub_agents=[],
  instruction='Use the UrlContextTool to retrieve content from provided URLs.',
  tools=[
    url_context
  ],
)
expense_cutter = LlmAgent(
  name='expense_cutter',
  model='gemini-3.1-pro-preview',
  description=(
      'Finds ways to reduce business expenses'
  ),
  sub_agents=[],
  instruction='You are a cost-reduction specialist. When a user has a low profit margin, look at their business type and suggest 5 ways to lower their monthly expenses without hurting their sales',
  tools=[
    agent_tool.AgentTool(agent=expense_cutter_google_search_agent),
    agent_tool.AgentTool(agent=expense_cutter_url_context_agent)
  ],
)
my_profit_advisor_google_search_agent = LlmAgent(
  name='My_Profit_Advisor_google_search_agent',
  model='gemini-3.1-pro-preview',
  description=(
      'Agent specialized in performing Google searches.'
  ),
  sub_agents=[],
  instruction='Use the GoogleSearchTool to find information on the web.',
  tools=[
    GoogleSearchTool()
  ],
)
my_profit_advisor_url_context_agent = LlmAgent(
  name='My_Profit_Advisor_url_context_agent',
  model='gemini-3.1-pro-preview',
  description=(
      'Agent specialized in fetching content from URLs.'
  ),
  sub_agents=[],
  instruction='Use the UrlContextTool to retrieve content from provided URLs.',
  tools=[
    url_context
  ],
)
root_agent = LlmAgent(
  name='My_Profit_Advisor',
  model='gemini-3.1-pro-preview',
  description=(
      'Calculates profit margins'
  ),
  sub_agents=[expense_cutter],
  instruction='You are a helpful financial advisor for small businesses. When a user gives you their revenue and costs, you will calculate their profit margin. You will then give them one short, friendly tip on how to improve it.',
  tools=[
    agent_tool.AgentTool(agent=my_profit_advisor_google_search_agent),
    agent_tool.AgentTool(agent=my_profit_advisor_url_context_agent)
  ],
)# Python Quickstart for ADK

This guide shows you how to get up and running with Agent Development Kit
(ADK) for Python. Before you start, make sure you have the following installed:

*   Python 3.10 or later
*   `pip` for installing packages

## Installation

Install ADK by running the following command:

```shell
pip install google-adk
```

??? tip "Recommended: create and activate a Python virtual environment"

    Create a Python virtual environment:

    ```shell
    python3 -m venv .venv
    ```

    Activate the Python virtual environment:

    === "Windows Command Prompt"

        ```console
        .venv\Scripts\activate.bat
        ```

    === "Windows PowerShell"

        ```console
        .venv\Scripts\Activate.ps1
        ```

    === "MacOS / Linux"

        ```bash
        source .venv/bin/activate
        ```

## Create an agent project

Run the `adk create` command to start a new agent project.

```shell
adk create my_agent
```

### Explore the agent project

The created agent project has the following structure, with the `agent.py`
file containing the main control code for the agent.

```none
my_agent/
    agent.py      # main agent code
    .env          # API keys or project IDs
    __init__.py
```

## Update your agent project

The `agent.py` file contains a `root_agent` definition which is the only
required element of an ADK agent. You can also define tools for the agent to
use. Update the generated `agent.py` code to include a `get_current_time` tool
for use by the agent, as shown in the following code:

```python
from google.adk.agents.llm_agent import Agent

# Mock tool implementation
def get_current_time(city: str) -> dict:
    """Returns the current time in a specified city."""
    return {"status": "success", "city": city, "time": "10:30 AM"}

root_agent = Agent(
    model='gemini-flash-latest',
    name='root_agent',
    description="Tells the current time in a specified city.",
    instruction="You are a helpful assistant that tells the current time in cities. Use the 'get_current_time' tool for this purpose.",
    tools=[get_current_time],
)
```

### Set your API key

This project uses the Gemini API, which requires an API key. If you
don't already have Gemini API key, create a key in Google AI Studio on the
[API Keys](https://aistudio.google.com/app/apikey) page.

In a terminal window, write your API key into an `.env` file as an environment variable:

=== "MacOS / Linux"

    ```bash title="Update: my_agent/.env"
    echo 'GOOGLE_API_KEY="YOUR_API_KEY"' > .env
    ```

=== "Windows PowerShell"

    ```console title="Update: my_agent/.env"
    echo 'GOOGLE_API_KEY="YOUR_API_KEY"' > .env
    ```

=== "Windows Command Prompt"

    ```console title="Update: my_agent/.env"
    echo GOOGLE_API_KEY="YOUR_API_KEY" > .env
    ```

??? tip "Using other AI models with ADK"
    ADK supports the use of many generative AI models. For more
    information on configuring other models in ADK agents, see
    [Models & Authentication](/agents/models).

## Run your agent

You can run your ADK agent with an interactive command-line interface using the
`adk run` command or the ADK web user interface provided by the ADK using the
`adk web` command. Both these options allow you to test and interact with your
agent.

### Run with command-line interface

Run your agent using the `adk run` command-line tool.

```console
adk run my_agent
```

![adk-run.png](/assets/adk-run.png)

### Run with web interface

The ADK framework provides web interface you can use to test and interact with
your agent. You can start the web interface using the following command:

```console
adk web --port 8000
```

!!! note

    Run this command from the **parent directory** that contains your
    `my_agent/` folder. For example, if your agent is inside `agents/my_agent/`,
    run `adk web` from the `agents/` directory.

This command starts a web server with a chat interface for your agent. You can
access the web interface at (http://localhost:8000). Select the agent at the
upper left corner and type a request.

![adk-web-dev-ui-chat.png](/assets/adk-web-dev-ui-chat.png)

!!! warning "Caution: ADK Web for development only"

    ADK Web is ***not meant for use in production deployments***. You should
    use ADK Web for development and debugging purposes only.

## Next: Build your agent

Now that you have ADK installed and your first agent running, try building
your own agent with our build guides:

*  [Build your agent](/tutorials/)
