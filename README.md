# Competitor price analyst

A GitHub Copilot **agent** and **skill** that turn a day's competitor price snapshots into
a short set of repricing recommendations a person can approve in a couple of minutes.

The agent recommends; a person decides. Nothing it produces goes live on its own, and it
never fetches a web page — it works only from snapshots the extraction agents captured
earlier in the run.

## What's here

```
.github/
    agents/
        price-analyst.agent.md              # the agent persona and standing rules
    skills/
        competitor-price-comparison/
            SKILL.md                        # the workflow: match, apply rules, validate, summarise
            references/
                price-snapshot-schema.md    # input contract — the competitor snapshot format
                output-spec.md              # output contract — recommendations.json + spreadsheet layout
                pricing-rules.json          # pricing policy: target position, rounding, change cap, exclusions
```

## How it works

1. **Merge** every competitor's `price-snapshot.json` into one view.
2. **Match** each catalogue item — exact (same brand and model number) or comparable (a
   judgment from specifications, carrying a confidence level).
3. **Apply the rules** in `pricing-rules.json` — lowest eligible price, target position, the
   cap on a single change, exclusions, rounding — never below the margin floor.
4. **Validate** with a script the agent writes and runs (blocking checks V1–V10).
5. **Produce** three outputs: `approval-summary.md` (the one a reviewer reads),
   `recommendations.json`, and `price-comparison.xlsx`.

## Inputs the caller supplies at run time

The agent instructions live here; the run-time data does not. A caller provides:

- **Price snapshots** — one per competitor, matching `references/price-snapshot-schema.md`.
- **The catalogue** — the products to price, carrying `sku`, `current_price`, `unit_cost`,
  `margin_floor_price` and `match_type`. Costs and margin floors are confidential and never
  appear in any output.
- **The rules** — `references/pricing-rules.json`.

## Using it in GitHub Copilot

Drop the `.github/agents` and `.github/skills` folders into a repository. Copilot discovers
the `price-analyst` agent and the `competitor-price-comparison` skill automatically; the
agent reads the skill at the start of each run.
