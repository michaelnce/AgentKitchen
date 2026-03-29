 1. Recipe Chef Bot

  Score: 9/10

  An agent that takes a dish name and orchestrates sub-agents to: (a) find ingredients, (b) find cooking steps, (c) estimate nutrition. A "format"
  skill combines everything into a nice recipe card.

  - Why it works: Everyone understands cooking. The decomposition is natural — ingredients, steps, and nutrition are clearly independent tasks that
  can run in parallel, then merge.
  - Agent: Orchestrator ("Chef")
  - Sub-agents: IngredientFinder, StepWriter, NutritionEstimator
  - Skill: FormatRecipeCard

    ┌──────────────────┬──────────────────────────────────────────────────────────────────────────────────────────┐
    │     Criteria     │                                       Recipe Chef                                        │
    ├──────────────────┼──────────────────────────────────────────────────────────────────────────────────────────┤
    │ Simplicity       │ Dead simple — 3 sub-agents, 1 skill, zero external APIs needed                           │
    ├──────────────────┼──────────────────────────────────────────────────────────────────────────────────────────┤
    │ Clarity          │ Anyone instantly understands why you'd split "ingredients" from "steps" from "nutrition" │
    ├──────────────────┼──────────────────────────────────────────────────────────────────────────────────────────┤
    │ Parallelism demo │ All 3 sub-agents run independently — perfect for showing fan-out/fan-in                  │
    ├──────────────────┼──────────────────────────────────────────────────────────────────────────────────────────┤
    │ Skill demo       │ The formatting skill is a clear, reusable transform — easy to explain                    │
    ├──────────────────┼──────────────────────────────────────────────────────────────────────────────────────────┤
    │ Audience         │ Universal — no domain expertise required                                                 │
    └──────────────────┴──────────────────────────────────────────────────────────────────────────────────────────┘
