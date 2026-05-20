# US Profession Dataset

Open dataset of US profession SCOPE profiles and AI-generated pain points. Output of the `profession-scan` operation.

## Counts

| Asset | Count | Size |
|---|---|---|
| `profiles/*.json` | 130 | ~2.5 MB |
| `pains/*.json` | 25 | ~870 KB |

130 profiles cover the most common US occupations sourced from O*NET OnLine. 25 of them already have generated pain bundles (calculators, checklists, templates, references, advisors). The remaining 105 are open for community contributions - run them through the `profession-scan` operation and PR the resulting pain files back.

## What's in a profile?

Each profile (`profiles/us-<slug>.json`) has 7 SCOPE-domain sections collected from US `.gov` sources, BLS, O*NET, professional associations, and Reddit communities:

1. **`daily_reality`** - typical workday, documents, deadlines, seasonality
2. **`regulatory`** - laws, licensing, penalties, regulatory bodies
3. **`tools`** - software, platforms, gov portals
4. **`terminology`** - jargon, abbreviations
5. **`career_psychology`** - career levels, fears, motivation, burnout signals
6. **`community`** - Reddit subs, LinkedIn groups, associations, conferences
7. **`market`** - salary by level (USD), job counts, growth rate, trends

## What's in a pain file?

Each pain file (`pains/us-<slug>.json`) contains 8-15 pain entries generated deductively from the SCOPE profile. Each pain has:

- `title`, `problem`, `who`, `frequency`, `time_waste_h`, `money_risk_usd`
- `skill_type` - one of `calculator | checklist | template | reference | advisor`
- `skill_spec` - structured spec that defines exactly what the AI skill would do (inputs, outputs, formulas, steps, etc.)
- `example_queries` - 2-3 sample user queries

See [_FORMAT.md](./_FORMAT.md) for the full schema.

## How was this generated?

Profiles came from a 7-query SCOPE batch per profession (BLS / `.gov` / Cornell LII / O*NET). Pain bundles came from feeding each SCOPE profile into a deductive pain-generation prompt run on Claude Opus.

The methodology is fully open - see `prompts/profession-scan/` and `skills/unfairgaps/references/profession-scan.md`. You can run it natively in Claude Code / Cursor with `/unfairgaps profession-scan <profession> in US` (no API key required).

## License

MIT. Use the dataset for any purpose - research, product development, content. Attribution appreciated.

## Contributing

Open a PR with:

- New pain files (run an existing profile through `profession-scan` and submit the output)
- Profile corrections (regulations change - fix outdated penalties, deadlines, rate references)
- New profiles for unlisted occupations
- Translations / non-US profiles (other countries coming - `data/professions/<cc>/`)
