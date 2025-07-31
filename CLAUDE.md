# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TradingAgents is a multi-agent LLM financial trading framework built with LangGraph. It simulates the dynamics of real-world trading firms by deploying specialized LLM-powered agents that collaboratively evaluate market conditions and make trading decisions.

## Installation and Setup

```bash
# Clone the repository
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents

# Create virtual environment
conda create -n tradingagents python=3.13
conda activate tradingagents

# Install dependencies
pip install -r requirements.txt

# Set up required API keys
export FINNHUB_API_KEY=your_finnhub_api_key
export OPENAI_API_KEY=your_openai_api_key
```

## Common Development Commands

### Run the CLI Interface
```bash
python -m cli.main
```

### Run Trading Simulation
```bash
python main.py
```

### Package Installation (Development Mode)
```bash
pip install -e .
```

## Architecture Overview

The framework follows a multi-agent architecture where each agent has specialized responsibilities:

### Core Agent Types
1. **Analyst Team** (`tradingagents/agents/analysts/`)
   - Market Analyst: Technical analysis using indicators (MACD, RSI)
   - Sentiment Analyst: Social media sentiment analysis
   - News Analyst: Global news and macroeconomic indicators
   - Fundamentals Analyst: Company financials and performance metrics

2. **Research Team** (`tradingagents/agents/researchers/`)
   - Bull Researcher: Optimistic market perspective
   - Bear Researcher: Pessimistic market perspective
   - Engage in structured debates to balance viewpoints

3. **Trader** (`tradingagents/agents/trader/`)
   - Synthesizes reports from analysts and researchers
   - Makes trading decisions based on comprehensive analysis

4. **Risk Management** (`tradingagents/agents/risk_mgmt/`)
   - Aggressive, Conservative, and Neutral debators
   - Risk Manager: Final risk assessment and portfolio decisions

### Key Components

- **Graph Structure** (`tradingagents/graph/`)
  - `trading_graph.py`: Main orchestration class
  - `propagation.py`: Message propagation through the agent network
  - `conditional_logic.py`: Decision flow control
  - `reflection.py`: Learning and memory mechanisms

- **Data Interfaces** (`tradingagents/dataflows/`)
  - Unified interface for multiple data sources (FinnHub, yFinance, Reddit, etc.)
  - Supports both online tools and cached data

- **Configuration** (`tradingagents/default_config.py`)
  - Centralized configuration for LLM providers, debate rounds, data directories
  - Supports OpenAI, Anthropic, and Google LLMs

### LangGraph Integration

The project uses LangGraph for agent orchestration with:
- State management through `AgentState`, `InvestDebateState`, and `RiskDebateState`
- Conditional edges for dynamic workflow
- Tool nodes for external API integration
- Memory persistence for learning from past decisions

## Key Usage Patterns

### Basic Trading Simulation
```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

ta = TradingAgentsGraph(debug=True, config=DEFAULT_CONFIG.copy())
_, decision = ta.propagate("NVDA", "2024-05-10")
```

### Custom Configuration
```python
config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "google"
config["deep_think_llm"] = "gemini-2.0-flash"
config["max_debate_rounds"] = 2
config["online_tools"] = True
```

## Important Notes

- The framework makes extensive API calls; use cheaper models (e.g., gpt-4o-mini) for testing
- Online tools provide real-time data; offline mode uses cached data from Tauric TradingDB
- The system supports reflection and learning through the `reflect_and_remember()` method
- All agents can be customized through their respective factory functions in the agents module