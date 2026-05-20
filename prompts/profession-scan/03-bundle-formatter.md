# Phase 3 - Bundle Formatter

Validate the Phase-2 output and serialize it as a final pain bundle compatible with the dataset in `data/professions/us/pains/`.

This phase is mostly mechanical - no new LLM reasoning needed if Phase 2 already returned clean JSON. Use this prompt only when the Phase-2 output has formatting drift (markdown fences, prose preambles, malformed `skill_spec` fields, etc.).

## Output filename

```
pains-<profession_id>-<YYYY-MM-DD>.json
```

Examples: `pains-us-lawyers-2026-05-20.json`, `pains-us-construction-managers-2026-05-20.json`.

## Validation checklist

Before saving, confirm:

1. Top-level value is a JSON **array** of pain objects (not wrapped in `{"pains": [...]}`)
2. Each pain has all required fields: `id`, `title`, `problem`, `who`, `frequency`, `time_waste_h`, `money_risk_usd`, `source`, `skill_type`, `skill_spec`, `example_queries`
3. `skill_type` is exactly one of: `calculator`, `checklist`, `template`, `reference`, `advisor`
4. `frequency` is exactly one of: `daily`, `weekly`, `monthly`, `quarterly`, `yearly`, `event`
5. `skill_spec` matches the per-type schema:
   - `calculator` -> `name`, `one_liner`, `inputs[]`, `outputs[]`, `formula_hint`, `rates{}`
   - `checklist` -> `name`, `one_liner`, `steps[]`, `warnings[]`, `regulatory_refs[]`
   - `template` -> `name`, `one_liner`, `doc_name`, `variables[]`, `structure_hint`
   - `reference` -> `name`, `one_liner`, `data_domain`, `lookup_keys[]`, `data_hints{}`
   - `advisor` -> `name`, `one_liner`, `expertise_area`, `decision_criteria[]`, `context_needed[]`
6. Pain count is 8-15
7. At least 3 distinct `skill_type` values across the bundle
8. `money_risk_usd` is a plain integer (no string, no range, no currency suffix)
9. `time_waste_h` is a plain integer
10. `example_queries` has 2-3 entries per pain

## Cleanup rules

- Strip markdown code fences (` ```json ` and ` ``` `) from start/end
- Remove preamble prose like "Here's the JSON..."
- Replace smart quotes with ASCII quotes
- Replace em-dashes with hyphens
- Ensure UTF-8, no BOM
- Pretty-print with 2-space indent

## Reference shape

See any file in `data/professions/us/pains/` for the canonical shape. Example: `us-lawyers.json`.

## Optional dataset registration

After saving the bundle, append the profession to the dataset index if you maintain one. The 25 pre-shipped pain bundles already cover:

```
accountants-and-auditors, acupuncturists, acute-care-nurses,
advanced-practice-psychiatric-nurses, aerospace-engineers,
agricultural-engineers, allergists-and-immunologists, ...
```

(Full list: `ls data/professions/us/pains/`)
