# US Pains - Format Reference

Output format of the `profession-scan` operation. One file per profession: `<profession_id>.json`.

## 5 skill types (`skill_type`)

### 1. `calculator`
Python-script or LLM-driven numeric calculator. Salary, fines, tax brackets, depreciation, dosage, area.
- **skill_spec**: `inputs[]`, `outputs[]`, `formula_hint`, `rates{}`

### 2. `checklist`
Step-by-step procedure with conditions and warnings. Pre-filing audit, document review, inspection prep.
- **skill_spec**: `steps[]`, `warnings[]`, `regulatory_refs[]`

### 3. `template`
LLM generates a document from a template with variables. Forms, motions, complaints, demand letters.
- **skill_spec**: `doc_name`, `variables[]`, `structure_hint`

### 4. `reference`
Structured lookup over regulation / rates / deadlines. State penalty tables, federal rates, filing deadlines.
- **skill_spec**: `data_domain`, `lookup_keys[]`, `data_hints{}`

### 5. `advisor`
LLM-powered Q&A with profession context. Case discussion, situational advice.
- **skill_spec**: `expertise_area`, `decision_criteria[]`, `context_needed[]`

## JSON shape

```json
[
  {
    "id": "us-lawyers-p01",
    "title": "Manual calculation of filing fees per jurisdiction",
    "problem": "Attorneys manually compute filing fees by court, case type, amount-in-controversy. Errors -> rejected filings, missed deadlines.",
    "who": "Civil litigators, small-firm attorneys",
    "frequency": "weekly | daily | monthly | quarterly | yearly | event",
    "time_waste_h": 2,
    "money_risk_usd": 500,
    "source": "daily_reality | regulatory | tools | career_psychology",
    "skill_type": "calculator",
    "skill_spec": { /* see above */ },
    "example_queries": [
      "Calculate filing fees for a civil suit in NY Supreme Court, $250K claim",
      "Federal court filing fees for a diversity action"
    ]
  }
]
```

## Generation rules

- 8-15 pains per profession
- Minimum 3 distinct `skill_type` per profession
- Priority: `calculator` > `checklist` > `template` > `reference` > `advisor`
- Each pain = a concrete, repeated task. NOT an abstract problem.
- `example_queries` = how a real user phrases it in chat
- US context: USD, IRS / OSHA / SEC / state regulators, federal + state law, MRP-equivalents replaced with US-specific rates (FICA 7.65%, Medicare 1.45%, federal tax brackets, etc.)
