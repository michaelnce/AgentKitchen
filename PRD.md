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
| **Web Research Agent** | An agent that uses tools to gather real-world data | `RecipeResearcher` searches online for top recipes and returns consensus findings |
| **Parallelism** | Sub-agents can run concurrently when their tasks are independent | All 4 sub-agents execute at the same time, then results are collected |
| **Review Agent** | A sequential quality gate that cross-references multiple data sources | `TopChef` merges generated recipe with web research to produce the best version |
| **Skill** | A reusable, composable transformation applied to data | `FormatRecipeCard` takes raw results and produces a formatted recipe card |

---

## 4. User Flow

```
User input:  "Spaghetti Carbonara"
                    │
                    ▼
           ┌───────────────┐
           │   ChefAgent   │  ← Orchestrator (custom slash command)
           │  (main agent) │
           └───────┬───────┘
                   │
     ┌─────────┼──────────┬──────────┐
     ▼         ▼          ▼          ▼
┌──────────┐ ┌────────┐ ┌─────────┐ ┌──────────┐
│Ingredient│ │  Step  │ │Nutrition│ │  Recipe  │  ← Sub-agents
│  Finder  │ │ Writer │ │Estimator│ │Researcher│    (spawned in parallel)
└────┬─────┘ └───┬────┘ └────┬────┘ └────┬─────┘
     │           │           │            │
     └───────────┼───────────┘            │
                 ▼                        │
       ┌─────────────────┐               │
       │ FormatRecipeCard │  ← Skill     │
       └────────┬────────┘               │
                ▼                         │
       ┌─────────────────┐               │
       │    TopChef      │◄──────────────┘  ← Cross-references research
       └────────┬────────┘
                ▼
        Improved Recipe
        saved to output/
```

---

## 5. Components

### 5.1 ChefAgent (Orchestrator)

- **Implementation:** Custom slash command in `.claude/commands/`
- **Input:** A dish name (string)
- **Responsibilities:**
  1. Parse the user request
  2. Spawn 4 sub-agents in parallel (using Claude Code's Agent tool): IngredientFinder, StepWriter, NutritionEstimator, RecipeResearcher
  3. Collect their results
  4. Apply the `FormatRecipeCard` skill to merge and format
  5. Send formatted recipe + research findings to `TopChef` review agent for cross-referencing and improvement
  6. Save the final recipe and execution log to `output/` as Markdown files
  7. Return the improved recipe to the user
- **Output:** Formatted recipe card (string)

### 5.2 Sub-Agents

Each sub-agent is a prompt-driven agent spawned by the orchestrator. They receive the dish name and return structured data.

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

### 5.3 RecipeResearcher (Web Research Agent)

- **Input:** Dish name
- **Output:** Structured JSON with sources, consensus ingredients, consensus techniques, pro tips, and controversies
- **Tools used:** WebSearch, WebFetch
- **Example output:**
  ```json
  {
    "sources": [
      {"name": "Serious Eats", "url": "https://...", "key_insight": "Use guanciale, never bacon"}
    ],
    "consensus_ingredients": ["guanciale", "egg yolks", "pecorino romano"],
    "consensus_techniques": ["Start guanciale in a cold pan", "Add egg mixture off heat"],
    "pro_tips": ["Reserve pasta water", "Warm your bowls"],
    "controversies": ["Whole eggs vs yolks only", "Pecorino only vs Pecorino-Parmesan blend"]
  }
  ```
- **Why a separate agent?** It demonstrates how agents can use external tools (web search) to ground their output in real-world data rather than relying solely on training knowledge.

### 5.4 TopChef (Review Agent)

- **Input:** Formatted recipe card (Markdown) + RecipeResearcher findings (JSON)
- **Output:** Improved recipe card that cross-references generated content with real-world research, adds a "Chef's Tips" and "Sources" section
- **Why a separate agent?** It demonstrates a sequential review pattern that merges multiple data sources — the generated recipe and web research — into a single refined output. This teaches the real-world pattern of quality gates in agent pipelines.

### 5.5 FormatRecipeCard (Skill)

- **Input:** Combined results from all 3 sub-agents + dish name
- **Output:** A human-readable, formatted recipe card (Markdown string)
- **Why a skill and not a sub-agent?** It's a pure transformation — no reasoning needed, just templating. This distinction is the teaching moment: skills are deterministic and reusable; sub-agents reason.

---

## 6. Tech Stack

| Layer | Choice | Why |
|-------|--------|-----|
| Platform | **Claude Code** (CLI / IDE) | The orchestration engine — agents, sub-agents, and skills are all Claude Code constructs |
| Configuration | **`.claude/` directory** | Custom slash commands, prompts, and project settings live here |
| Orchestration | **Claude Code Agent tool** | Spawns sub-agents natively — no custom code needed |
| Language | **None (prompt-only)** | No Python, no framework. The entire system is prompt-driven. Code only if strictly needed |
| Output | **Terminal / Claude Code UI** | Results displayed directly in the conversation |

---

## 7. File Structure

```
AgentKitchen/
├── PRD.md                          ← this file
├── pitch.md                        ← original project pitch
├── README.md                       ← project overview
├── CLAUDE.md                       ← project instructions for Claude Code
└── .claude/
    └── commands/
        ├── agent-chef.md                 ← orchestrator command: /agent-chef "dish name"
        ├── subagent-find-ingredients.md  ← sub-agent prompt
        ├── subagent-write-steps.md       ← sub-agent prompt
        ├── subagent-estimate-nutrition.md ← sub-agent prompt
        ├── subagent-recipe-researcher.md  ← web research agent prompt
        ├── subagent-top-chef.md          ← review agent prompt
        └── skill-format-recipe.md        ← skill prompt (formatting only)
```

---

## 8. Key Design Decisions

### 8.1 No code on purpose
The entire system runs inside Claude Code using prompts and commands. This keeps the focus on agent patterns without any programming language getting in the way. Code is only added if strictly necessary.

### 8.2 Skill is a formatting prompt, not a reasoning task
The `FormatRecipeCard` skill takes structured data and produces a Markdown card. It demonstrates that skills are deterministic transforms, distinct from sub-agents that reason.

### 8.3 Structured output from sub-agents
Each sub-agent returns JSON-structured data. This teaches a critical real-world pattern: agents communicate through contracts, not free-form text.

### 8.4 Custom slash commands as the interface
The user types `/agent-chef "Spaghetti Carbonara"` and the orchestrator handles everything. This makes the entry point intuitive and demonstrates how Claude Code commands work.

---

## 9. Success Criteria

1. A user can run `/agent-chef "Spaghetti Carbonara"` in Claude Code and see a formatted recipe card
2. The command files are readable in under 5 minutes
3. The three patterns (agent, sub-agent, skill) are clearly visible in the command structure
4. A user can swap in a different dish name and get a different recipe — no hardcoding

---

## 10. Out of Scope

- Python application or web server
- Database or caching
- Error retries or fallback logic
- External API integrations
- User authentication

These can be added as "next steps" in the video but are not part of v1.

---

## 11. Future Extensions (teased in video, not built)

- Add a `DietaryFilter` skill that removes ingredients based on allergies
- Add a `CostEstimator` sub-agent
- Chain multiple skills: format → translate → export-to-PDF
- Wrap the system in a Python CLI using the Anthropic Agent SDK
