# AgentKitchen — Recipe Chef Bot

This is an educational project demonstrating Agent, Sub-Agent, and Skill patterns using Claude Code.

## How it works

- `/agent-chef <dish name>` is the main entry point — it orchestrates everything
- Four sub-agents run in parallel: IngredientFinder, StepWriter, NutritionEstimator, RecipeResearcher
- A formatting skill merges the results into a Markdown recipe card
- A TopChef agent reviews and improves the recipe before final output
- No application code — everything is prompt-driven via `.claude/commands/`

## Commands

| Command | Role | Type |
|---------|------|------|
| `/agent-chef` | Orchestrator — coordinates sub-agents and formats output | Agent |
| `/subagent-find-ingredients` | Returns JSON list of ingredients with quantities | Sub-agent |
| `/subagent-write-steps` | Returns JSON list of cooking steps | Sub-agent |
| `/subagent-estimate-nutrition` | Returns JSON nutrition info per serving | Sub-agent |
| `/subagent-recipe-researcher` | Searches the web for top recipes and returns consensus findings | Sub-agent |
| `/subagent-top-chef` | Reviews and improves the recipe using generated data + web research | Sub-agent |
| `/skill-format-recipe` | Formats raw JSON data into a Markdown recipe card | Skill |
