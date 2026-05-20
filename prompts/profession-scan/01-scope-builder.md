# Phase 1 - SCOPE Builder (country-aware)

Build a structured profile of a profession in a target country across 7 regulatory + occupational domains. The output is a JSON blob that feeds the pain generator in Phase 2.

**Inputs:** `{profession}` (free-text occupation name) and `{country}` (ISO 2-letter code or full name: US, DE, KZ, UK, FR, BR, ...).

## Country adaptation - mandatory pre-step

Before composing any queries, infer country context. The user's `{country}` controls:

- **Primary languages for retrieval.** US -> [en]. DE -> [de, en]. KZ -> [ru, kk]. FR -> [fr]. JP -> [ja]. UK -> [en]. CN -> [zh]. RU -> [ru]. BR -> [pt]. If you only run English queries on a non-English country, you'll get fake "no evidence". This is non-negotiable.
- **Regulatory bodies.** For US: IRS, OSHA, SEC, FTC, FDA, EPA, DOL, state boards. For DE: BMAS, BAFin, BAuA, Berufsgenossenschaften, Bundesnetzagentur, Finanzamt. For UK: HMRC, HSE, FCA, GMC, SRA. For KZ: KGD, MIO, Минтруда, МЗ РК, Палата адвокатов. For FR: URSSAF, DGFiP, Inspection du travail, Ordres professionnels. Etc.
- **Statutory citation style.** US: USC, CFR, state statutes. DE: BGB, StGB, EStG, Gewerbeordnung. UK: Acts of Parliament, statutory instruments. KZ: НК РК, КоАП РК, ТК РК. FR: Code du travail, Code général des impôts. Etc.
- **Currency for pain financial impact.** Use USD by default in the final pain output (Phase 2) for cross-country comparison. In the SCOPE profile (this phase), keep amounts in local currency with `currency` field and add `usd_estimate` if helpful.
- **Trusted domains.** Score `.gov` analog for the target country higher. Examples:
  - US: `.gov` (irs.gov, osha.gov, sec.gov, bls.gov), `law.cornell.edu`, `onetonline.org`, `congress.gov`
  - UK: `.gov.uk` (hmrc.gov.uk, hse.gov.uk, legislation.gov.uk), `ons.gov.uk`
  - DE: `.bund.de`, `.de` state portals, `gesetze-im-internet.de`, `destatis.de`
  - FR: `.gouv.fr` (impots.gouv.fr, legifrance.gouv.fr, urssaf.fr), `insee.fr`
  - KZ: `.gov.kz` (kgd.gov.kz, adilet.zan.kz), `enbek.kz`
  - EU-level: `.europa.eu`, `eur-lex.europa.eu`
  - Always: primary professional associations of that country (AICPA US, IDW DE, ICAEW UK, etc.)
- **Career level naming.** US: associate -> partner. DE: Junior -> Senior -> Geschäftsführer. UK: trainee -> partner. KZ: помощник -> старший -> партнёр. Etc.

If you cannot confidently determine country-specific regulators for an unusual country (e.g., Tajikistan, Eswatini), proceed honestly: run general queries, mark sections as `coverage_gap: true`, and note which native-language regulators you searched for but could not retrieve.

## System prompt (use verbatim)

```
You are a research analyst specializing in the {country} labor market.
Respond ONLY with valid JSON. No text before or after the JSON.
Always include sources - an array of objects with fields:
"name" (source name), "url" (link), "fact" (specific fact from that source).
All facts must be specific: numbers, dates, document names.
For non-English countries, run queries in the native language(s) and include sources in that language.
```

## 7 SCOPE queries (run via WebSearch sequentially)

Substitute both `{profession}` (occupation, e.g., "Lawyers", "Electricians", "Accountants") and `{country}` (country name, e.g., "United States", "Germany", "Kazakhstan", "United Kingdom"). Translate the query into native language(s) if the country is non-English (run native-language pass + an English-language pass for international coverage).

For each query, run 1-2 `WebSearch` calls then 2-4 `WebFetch` calls on the highest-scoring trusted-domain results. Compress findings into the section of the SCOPE profile shown below.

### Query 1 - daily_reality

```
Typical workday of a {profession} in {country} 2025-2026:
tasks by hour from start to end of the workday,
what documents and forms are completed daily/weekly/monthly,
key deadlines (quarterly filings, annual reports, one-time),
seasonal workload (which months are peak and why),
who they interact with inside the organization and externally
(regulators, clients, contractors, courts).
Need a detailed answer with specifics: form names, filing deadlines,
real examples from {country} practitioners.
```

### Query 2 - regulatory

```
{profession} {country} regulatory environment 2025-2026:
current federal/national and regional/state laws that directly affect the work
(specific statutory citations in the local format),
regulatory and licensing bodies and their authority,
types of inspections and audits (scheduled, random, triggered),
penalties for typical violations (specific monetary amounts in local currency
plus USD equivalent, statutory references),
new regulations enacted or effective in 2025-2026,
licensing, certification, and continuing education requirements
(which regions/states require what, reciprocity agreements).
```

### Query 3 - tools

```
{profession} {country} what software and tools used at work 2025-2026:
primary professional software (name the actual products used in {country}),
government portals and filing systems (the country's actual e-gov systems),
industry-specific systems (name, purpose),
spreadsheets and custom templates (for what tasks),
problems with current software (bugs, UX issues, integration gaps, cost),
communication tools.
```

### Query 4 - terminology

```
{profession} {country} professional jargon, slang and abbreviations 2025-2026:
how practitioners refer to key processes and documents among themselves
(in the local language plus English equivalents),
what words they use to describe their problems on forums and in chats,
common abbreviations and acronyms specific to this profession in {country},
examples of typical questions asked to colleagues or in professional forums
(local-language forums, Reddit, LinkedIn groups, industry channels).
```

### Query 5 - career_psychology

```
{profession} {country} career and professional psychology 2025-2026:
career levels using local naming conventions
(e.g., associate -> partner in US/UK, Junior -> Senior in DE, etc.),
how responsibilities differ at each level,
main fears (malpractice, audit, license revocation, lawsuit, reputation damage),
growth motivation (salary increase, certification, starting own practice, partner track),
burnout causes (overwork, billable hours pressure, regulatory burden, toxic management),
common mistakes by early-career professionals and their consequences.
```

### Query 6 - community

```
{profession} {country} professional communities and channels 2025-2026:
Reddit communities (subreddit names, subscriber count),
local-language forums and professional networks,
LinkedIn groups and influencers in this field in {country},
YouTube channels with educational content,
professional associations and organizations
(name the actual {country} associations - mandatory or voluntary, members, website),
conferences and events (major annual events, CLE/CPE/CPD providers),
Slack/Discord/Telegram communities, industry newsletters.
```

### Query 7 - market

```
{profession} {country} labor market 2025-2026:
salary by level in local currency plus USD equivalent
(entry, mid, senior, director/partner) - major cities vs national average,
estimated number of practitioners in {country} (cite the country's stats bureau),
job openings on local job boards (Indeed, LinkedIn, headhunter equivalents),
work arrangements: employee, contractor, partnership, solo practice
- which dominate and why,
average billing rate or service fee for private practice (if applicable),
market trends: growing demand, automation impact, AI disruption of the profession.
```

## Output schema

Save as `scope-<profession_id>-<YYYY-MM-DD>.json` in the current working directory. Profession_id format: `<cc>-<slug>`. Examples: `us-lawyers`, `de-elektriker`, `kz-advokat`, `uk-solicitors`, `fr-avocats`.

```json
{
  "profession_id": "<cc>-<slug>",
  "profession_name": "<canonical occupation name>",
  "country_code": "<ISO-2>",
  "country_name": "<full>",
  "primary_languages": ["<language codes used for retrieval>"],
  "tier": "A | B | C (optional - your subjective importance ranking)",
  "currency": "<ISO 4217, e.g., USD, EUR, KZT>",
  "daily_reality": {
    "typical_workday": "...",
    "documents": ["..."],
    "deadlines": ["..."],
    "seasonality": "..."
  },
  "regulatory": {
    "laws": ["<statute name + local citation format>"],
    "licensing": "...",
    "penalties": ["<amount + currency + USD equivalent + statutory ref>"],
    "regulatory_bodies": ["..."]
  },
  "tools": {
    "software": ["..."],
    "platforms": ["..."],
    "gov_portals": ["..."],
    "pain_points": ["..."]
  },
  "terminology": {
    "jargon": ["..."],
    "abbreviations": ["..."]
  },
  "career_psychology": {
    "career_levels": ["..."],
    "fears": ["..."],
    "motivation": ["..."],
    "burnout": "..."
  },
  "community": {
    "reddit": ["..."],
    "forums": ["..."],
    "linkedin": ["..."],
    "associations": ["..."],
    "conferences": ["..."]
  },
  "market": {
    "salary_by_level_local": { "entry": "...", "mid": "...", "senior": "...", "partner": "..." },
    "salary_by_level_usd": { "entry": "...", "mid": "...", "senior": "...", "partner": "..." },
    "job_count": "...",
    "growth_rate": "...",
    "trends": ["..."]
  },
  "sources": [
    { "name": "...", "url": "https://...", "fact": "...", "language": "<en|de|...>" }
  ],
  "coverage_gaps": ["<what you searched for but couldn't find; be honest>"]
}
```

For a complete reference example, see `data/professions/us/profiles/us-lawyers.json` (US) - structure transfers to any country with currency + language adjustments.
