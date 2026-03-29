# PRD: Recipe Chef Bot

## 1. Purpose

A minimal educational project that demonstrates how **Agent**, **Sub-Agent**, and **Skill** patterns work together in an AI system. The audience is developers (or aspiring developers) watching a video tutorial — every design choice prioritizes clarity over sophistication.

---

## 2. Problem Statement

The concepts of agent orchestration, sub-agent delegation, and reusable skills are abstract and hard to grasp from documentation alone. Learners need a concrete, relatable example they can run, read, and modify to build intuition.

---

## 3. Core Concepts Demonstrated

| Concept | What it teaches | How the project shows it |
|---------|----------------|--------------------------|
| **Agent** (Orchestrator) | A central coordinator that breaks a task into parts and merges results | The `ChefAgent` receives a dish name, delegates work, and assembles the final recipe |
| **Sub-Agent** | Specialized workers that run independently and return structured results | `IngredientFinder`, `StepWriter`, `NutritionEstimator` — each does one job |
| **Parallelism** | Sub-agents can run concurrently when their tasks are independent | All 3 sub-agents execute at the same time, then results are collected |
| **Skill** | A reusable, composable transformation applied to data | `FormatRecipeCard` takes raw results and produces a formatted recipe card |

---

## 4. User Flow

```
User input:  "Spaghetti Carbonara"
                    │
                    ▼
           ┌───────────────┐
           │   ChefAgent   │  ← Orchestrator
           │  (main agent) │
           └───────┬───────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
  ┌───────────┐ ┌─────────┐ ┌──────────────┐
  │ Ingredient│ │  Step   │ │  Nutrition   │  ← Sub-agents
  │  Finder   │ │ Writer  │ │  Estimator   │     (parallel)
  └─────┬─────┘ └────┬────┘ └──────┬───────┘
        │            │             │
        └──────────┬─┘─────────────┘
                   ▼
         ┌─────────────────┐
         │ FormatRecipeCard │  ← Skill
         └────────┬────────┘
                  ▼
          Formatted Recipe
          shown to user
```

---

## 5. Components

### 5.1 ChefAgent (Orchestrator)

- **Input:** A dish name (string)
- **Responsibilities:**
  1. Parse the user request
  2. Spawn 3 sub-agents in parallel
  3. Collect their results
  4. Pass combined results to the `FormatRecipeCard` skill
  5. Return the formatted recipe to the user
- **Output:** Formatted recipe card (string)

### 5.2 Sub-Agents

Each sub-agent receives the dish name and returns structured data.

#### IngredientFinder
- **Input:** Dish name
- **Output:** List of ingredients with quantities
- **Example output:**
  ```json
  {
    "ingredients": [
      { "name": "Spaghetti", "quantity": "400g" },
      { "name": "Guanciale", "quantity": "200g" },
      { "name": "Egg yolks", "quantity": "4" },
      { "name": "Pecorino Romano", "quantity": "100g" },
      { "name": "Black pepper", "quantity": "to taste" }
    ]
  }
  ```

#### StepWriter
- **Input:** Dish name
- **Output:** Ordered list of cooking steps
- **Example output:**
  ```json
  {
    "steps": [
      "Boil a large pot of salted water and cook spaghetti until al dente.",
      "Cut guanciale into small strips and cook in a dry pan until crispy.",
      "Mix egg yolks with grated Pecorino and black pepper in a bowl.",
      "Drain pasta, reserving 1 cup of pasta water.",
      "Toss hot pasta with guanciale, then quickly stir in egg mixture off heat.",
      "Add pasta water as needed for a creamy sauce. Serve immediately."
    ]
  }
  ```

#### NutritionEstimator
- **Input:** Dish name
- **Output:** Estimated nutrition per serving
- **Example output:**
  ```json
  {
    "servings": 4,
    "per_serving": {
      "calories": 650,
      "protein": "28g",
      "carbs": "72g",
      "fat": "26g"
    }
  }
  ```

### 5.3 FormatRecipeCard (Skill)

- **Input:** Combined results from all 3 sub-agents + dish name
- **Output:** A human-readable, formatted recipe card (Markdown string)
- **Why a skill and not a sub-agent?** It's a pure transformation — no LLM reasoning needed, just templating. This distinction is the teaching moment: skills are deterministic and reusable; sub-agents reason.

---

## 6. Tech Stack

| Layer | Choice | Why |
|-------|--------|-----|
| Language | **Python 3.11+** | Most accessible for the tutorial audience |
| LLM | **Claude API** (via `anthropic` SDK) | Powers the sub-agents' reasoning |
| Framework | **None** — plain Python | No magic, no framework to learn. Every line is visible and explainable |
| Concurrency | **`asyncio.gather`** | Simplest way to show parallel sub-agent execution |
| Output | **Terminal / stdout** | No web UI needed — keeps focus on the patterns |

---

## 7. File Structure

```
simpleAgentics/
├── PRD.md                  ← this file
├── pitch.md                ← original project pitch
├── main.py                 ← entry point: takes user input, runs ChefAgent
├── agents/
│   ├── __init__.py
│   ├── chef_agent.py       ← orchestrator
│   ├── ingredient_finder.py
│   ├── step_writer.py
│   └── nutrition_estimator.py
├── skills/
│   ├── __init__.py
│   └── format_recipe_card.py
├── models/
│   ├── __init__.py
│   └── recipe.py           ← data classes for structured results
├── requirements.txt
└── .env.example            ← template for ANTHROPIC_API_KEY
```

---

## 8. Key Design Decisions

### 8.1 No framework on purpose
Frameworks hide the orchestration logic. Since the goal is to *teach* orchestration, every dispatch, await, and merge is explicit in the code.

### 8.2 Skill is pure Python, not an LLM call
The `FormatRecipeCard` skill is intentionally a plain function (no API call). This makes the distinction crystal clear:
- **Sub-agent** = uses LLM to reason
- **Skill** = deterministic code that transforms data

### 8.3 Structured output from sub-agents
Each sub-agent returns JSON-parseable structured data (via Pydantic models). This teaches a critical real-world pattern: agents communicate through contracts, not free-form text.

### 8.4 Parallel execution is visible
The code explicitly uses `asyncio.gather()` so the learner can see the fan-out and fan-in in a single line.

---

## 9. Success Criteria

1. A viewer can run `python main.py "Spaghetti Carbonara"` and see a formatted recipe card
2. The code is readable top-to-bottom in under 10 minutes
3. Each file is under 50 lines of code
4. The three patterns (agent, sub-agent, skill) are clearly labeled in code comments
5. A viewer can swap in a different dish name and get a different recipe — no hardcoding

---

## 10. Out of Scope

- Web UI or API server
- Database or caching
- Error retries or fallback logic
- Multiple skill chaining
- User authentication
- Streaming responses

These can be added as "next steps" in the video but are not part of v1.

---

## 11. Future Extensions (teased in video, not built)

- Add a `DietaryFilter` skill that removes ingredients based on allergies
- Add a `CostEstimator` sub-agent
- Chain multiple skills: format → translate → export-to-PDF
- Replace Claude with a local model to show provider-agnostic design
