# AgentKitchen — Recipe Chef Bot

A minimal educational project that demonstrates how **Agent**, **Sub-Agent**, and **Skill** patterns work together in an AI system — built entirely inside **Claude Code** with zero application code.

Give it a dish name, and it orchestrates four specialized sub-agents — including one that researches real recipes online — to generate the best possible recipe card.

```
/agent-chef "Spaghetti Carbonara"
              │
              ▼
       ┌─────────────┐
       │  ChefAgent  │  ← Orchestrator (slash command)
       └──────┬──────┘
              │
    ┌─────────┼─────────┬──────────┐
    ▼         ▼         ▼          ▼
Ingredient   Step    Nutrition   Recipe       ← Sub-agents (spawned in parallel)
 Finder     Writer   Estimator  Researcher
    │         │         │          │
    └─────────┼─────────┘          │
              ▼                    │
              ▼
      FormatRecipeCard             ← Skill (formatting prompt)
              │
              ▼
     TopChef  ◄────────────────────┘  ← Review agent (cross-references research)
              │
              ▼
       Improved Recipe
      (saved to output/)
```

## What It Teaches

| Concept | How the project shows it |
|---------|--------------------------|
| **Agent (Orchestrator)** | `/agent-chef` command receives a dish name, delegates work, and assembles the final recipe |
| **Sub-Agents** | `/subagent-find-ingredients`, `/subagent-write-steps`, `/subagent-estimate-nutrition` — each does one job independently |
| **Web Research Agent** | `/subagent-recipe-researcher` searches online for top recipes and returns consensus findings |
| **Review Agent** | `/subagent-top-chef` cross-references generated recipe with web research and improves it |
| **Parallelism** | Sub-agents are spawned concurrently via Claude Code's Agent tool |
| **Skill** | `/skill-format-recipe` is a formatting-only prompt (no reasoning) that transforms structured data into a recipe card |

## Tech Stack

- **Claude Code** — the orchestration engine; agents, sub-agents, and skills are all Claude Code constructs
- **`.claude/commands/`** — custom slash commands defined as markdown prompt files
- **No application code** — the entire system is prompt-driven

## Project Structure

```
AgentKitchen/
├── CLAUDE.md                       # Project instructions for Claude Code
└── .claude/
    └── commands/
        ├── agent-chef.md                 # Orchestrator: /agent-chef "dish name"
        ├── subagent-find-ingredients.md  # Sub-agent prompt
        ├── subagent-write-steps.md      # Sub-agent prompt
        ├── subagent-estimate-nutrition.md # Sub-agent prompt
        ├── subagent-recipe-researcher.md # Web research agent prompt
        ├── subagent-top-chef.md         # Review agent prompt
        └── skill-format-recipe.md       # Skill prompt (formatting only)
```

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- An Anthropic API key configured

### Setup

```bash
git clone https://github.com/michaelnce/AgentKitchen.git
cd AgentKitchen
```

### Run

Open Claude Code in the project directory and type:

```
/agent-chef Spaghetti Carbonara
```

You'll see a chef-reviewed recipe card with ingredients, cooking steps, pro tips, and nutrition info — saved to `output/`.

### Examples

```bash
# Classic Italian
/agent-chef Spaghetti Carbonara

# Asian cuisine
/agent-chef Pad Thai

# Baking
/agent-chef Sourdough Bread

# French classic
/agent-chef Beef Bourguignon

# Quick weeknight meal
/agent-chef Chicken Stir Fry
```

Each run produces two files in `output/`:
- `output/<dish-name>.md` — the final recipe card
- `output/<dish-name>.log.md` — execution log showing each agent's contribution

## Key Design Decisions

**No code on purpose.** The entire system runs inside Claude Code using prompts and commands. This keeps the focus on agent patterns without any programming language getting in the way.

**Skill vs. Sub-agent distinction.** The `format-recipe` skill is a formatting prompt — no reasoning needed. Sub-agents use LLM reasoning to generate content; skills are deterministic transforms. This distinction is the core teaching moment.

**Web research agent.** The `RecipeResearcher` agent searches real recipes online and returns consensus findings — demonstrating how agents can use tools (WebSearch, WebFetch) to ground their output in real-world data.

**Review agent pattern.** The `TopChef` agent cross-references the generated recipe with web research findings, demonstrating a sequential quality gate that combines multiple data sources.

**Structured output.** Sub-agents return JSON, teaching the real-world pattern of agents communicating through contracts, not free-form text.

**Custom slash commands as the interface.** The user types `/agent-chef "Spaghetti Carbonara"` and the orchestrator handles everything — intuitive and demonstrates how Claude Code commands work.

## Future Extensions

Ideas for learners to explore after the tutorial:

- `DietaryFilter` skill — remove ingredients based on allergies
- `CostEstimator` sub-agent — estimate recipe cost
- Skill chaining: format → translate → export-to-PDF
- Wrap the system in a Python CLI using the Anthropic Agent SDK
