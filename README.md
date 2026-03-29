# AgentKitchen — Recipe Chef Bot

A minimal educational project that demonstrates how **Agent**, **Sub-Agent**, and **Skill** patterns work together in an AI system. Built as a companion to a video tutorial, every design choice prioritizes clarity over sophistication.

Give it a dish name, and it orchestrates three specialized sub-agents in parallel to generate a complete recipe card.

```
"Spaghetti Carbonara"
          │
          ▼
   ┌─────────────┐
   │  ChefAgent   │  ← Orchestrator
   └──────┬──────┘
          │
   ┌──────┼──────┐
   ▼      ▼      ▼
Ingredient  Step   Nutrition     ← Sub-agents (parallel)
 Finder    Writer  Estimator
   │      │      │
   └──────┼──────┘
          ▼
  FormatRecipeCard               ← Skill (pure Python)
          │
          ▼
   Formatted Recipe
```

## What It Teaches

| Concept | How the project shows it |
|---------|--------------------------|
| **Agent (Orchestrator)** | `ChefAgent` receives a dish name, delegates work, and assembles the final recipe |
| **Sub-Agents** | `IngredientFinder`, `StepWriter`, `NutritionEstimator` — each does one job independently |
| **Parallelism** | All 3 sub-agents execute concurrently via `asyncio.gather()`, then results are collected |
| **Skill** | `FormatRecipeCard` is a pure Python function (no LLM call) that transforms structured data into a formatted recipe card |

## Tech Stack

- **Python 3.11+** — accessible for tutorial audiences
- **Claude API** (via `anthropic` SDK) — powers sub-agent reasoning
- **asyncio** — parallel sub-agent execution
- **No framework** — all orchestration logic is explicit and visible

## Project Structure

```
AgentKitchen/
├── main.py                     # Entry point
├── agents/
│   ├── chef_agent.py           # Orchestrator
│   ├── ingredient_finder.py    # Sub-agent: ingredients + quantities
│   ├── step_writer.py          # Sub-agent: cooking steps
│   └── nutrition_estimator.py  # Sub-agent: nutrition per serving
├── skills/
│   └── format_recipe_card.py   # Skill: formats results into a recipe card
├── models/
│   └── recipe.py               # Pydantic data classes
├── requirements.txt
└── .env.example                # Template for API key
```

## Getting Started

### Prerequisites

- Python 3.11+
- An [Anthropic API key](https://console.anthropic.com/)

### Setup

```bash
# Clone the repo
git clone <repo-url>
cd AgentKitchen

# Create a virtual environment
python -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Configure your API key
cp .env.example .env
# Edit .env and add your ANTHROPIC_API_KEY
```

### Run

```bash
python main.py "Spaghetti Carbonara"
```

You'll see a formatted recipe card with ingredients, cooking steps, and nutrition info.

## Key Design Decisions

**No framework on purpose.** Frameworks hide orchestration logic. Since the goal is to *teach* orchestration, every dispatch, await, and merge is explicit in the code.

**Skill vs. Sub-agent distinction.** The `FormatRecipeCard` skill is a plain function — no API call. Sub-agents use LLM reasoning; skills are deterministic transformations. This distinction is the core teaching moment.

**Structured output.** Sub-agents return JSON via Pydantic models, teaching the real-world pattern of agents communicating through contracts, not free-form text.

## Future Extensions

Ideas for learners to explore after the tutorial:

- `DietaryFilter` skill — remove ingredients based on allergies
- `CostEstimator` sub-agent — estimate recipe cost
- Skill chaining: format → translate → export-to-PDF
- Swap Claude for a local model to demonstrate provider-agnostic design
