# BuilderGraph

The builder's agent — verified developer profiles from public GitHub activity, matched to opportunities.

BuilderGraph scans what you've actually built (not what you claim on a resume), creates a structured profile, and matches you to opportunities based on real code, real frameworks, and real shipping patterns.

## Quick Start

```bash
# 1. Scan a GitHub profile
node buildergraph/cli.mjs profile <github-username>

# 2. Add private context (projects not on GitHub)
node buildergraph/cli.mjs enrich <github-username>

# 3. Generate a shareable builder card (HTML + PDF)
node buildergraph/cli.mjs card <github-username>

# 4. Score an opportunity against your profile
node buildergraph/cli.mjs match <jd-file.md> --user=<github-username>
```

## Commands

### `profile <username>`

Scans a public GitHub profile and builds a structured developer profile:
- Languages by bytes of code (sorted by usage)
- Frameworks detected from package.json, requirements.txt
- Project list with commit activity and descriptions
- Builder type classification (Full-Stack, AI/ML, CLI/Tools, Infrastructure, Prolific)
- Activity signals (active repos, recent commits, account age)

Saves to `data/<username>-profile.json`.

### `enrich [username]`

Interactive prompts to add context not visible on GitHub:
- Private projects (name, description, tech stack, impact)
- Work preferences (what you're looking for, industries, dealbreakers)
- Availability (status, hours/week, rate, start date, location)

Saves to `data/<username>-enriched.json`.

### `match <jd-file> [--user=username]`

Scores a job description or project brief against your profile (0-5 scale):
- Required skills match (40%)
- Nice-to-have skills (15%)
- Project relevance (25%)
- Activity signals (10%)
- Builder type alignment (10%)

Accepts any markdown file with a title and skills sections.

### `card [username]`

Generates a shareable builder card:
- Dark-themed HTML profile card with language bars, framework tags, project grid, and signals
- PDF version via Playwright (if installed)

Output in `output/<username>-builder-card.html` and `.pdf`.

## GitHub API Rate Limits

Without authentication: 60 requests/hour (enough for ~3-4 profile scans).

Set a GitHub personal access token for 5,000 requests/hour:

```bash
export GITHUB_TOKEN=ghp_your_token_here
```

No special scopes needed — public data only.

## Tech Stack

- Node.js (ESM)
- GitHub REST API
- Playwright (PDF generation)
- No external dependencies beyond Playwright

## Project Structure

```
buildergraph/
  cli.mjs              Entry point
  commands/
    profile.mjs        Scan GitHub profile
    enrich.mjs         Add private context
    match.mjs          Score opportunities
    card.mjs           Generate builder card
  lib/
    github.mjs         GitHub API client
    profile-builder.mjs Profile aggregation
    matcher.mjs        Scoring engine
  templates/
    builder-card.html  Card HTML template
  tests/
    github.test.mjs
    profile-builder.test.mjs
    matcher.test.mjs
  data/                Profile + enrichment JSON (gitignored)
  output/              Generated cards (gitignored)
```

## Running Tests

```bash
node --test buildergraph/tests/
```

Note: `github.test.mjs` and `profile-builder.test.mjs` hit the live GitHub API. Set `GITHUB_TOKEN` to avoid rate limits in CI.
