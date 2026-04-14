# npm-readme-skill

> Sophisticated AI skill for generating consistent, high-quality READMEs for npm packages.

When publishing an npm package, the README is the first thing users see on npmjs.com. This skill generates professional, consistent READMEs with minimal input from you.

## Quick Start

Add the skill to your npm project by downloading the two files into your repo:

```bash
mkdir -p .github/skills/npm-readme-skill
curl -sO --output-dir .github/skills/npm-readme-skill \
  https://raw.githubusercontent.com/tsmx/npm-readme-skill/master/SKILL.md
curl -sO --output-dir .github/skills/npm-readme-skill \
  https://raw.githubusercontent.com/tsmx/npm-readme-skill/master/README-template.md
```

Then open your AI chat and ask:

```
Create a README for my npm package
```

The skill auto-discovers your project, proposes optional sections, optimizes for npmjs.com search, generates `README.md`, and validates the result.

For other AI tools (Claude Code, Cursor, etc.), place `SKILL.md` and `README-template.md` in your tool's custom instructions folder.

## Features

- Project auto-discovery — reads `package.json`, git config, CI workflows, test setup, and git history
- Badge management — evaluates badge coverage and offers improvements
- Optional section detection — identifies Setup, Options, Changelog, SBOM, Security, CLI, and more from project signals
- npm search optimization — researches top-ranking packages for your target terms and proposes `package.json` `description`, `keywords`, tagline, and intro wording for better discoverability
- Create and update modes — generates from scratch or merges into an existing README without losing custom content
- Post-generation validation — checks for leftover placeholders, missing sections, API formatting, and typos before delivery

## Files in This Skill

| File | Purpose |
|------|---------|
| SKILL.md | The skill definition — generation procedure, rules, and formatting standards |
| README-template.md | The README template for npm libraries, with inline `<!-- AI: ... -->` guidance per section |

## How It Works

1. Read the template and internalize all generation rules
2. Scan the project — package identity, repo, CI, tests, module format, optional section signals
3. Evaluate badge coverage; offer to fill gaps
4. Identify and propose optional sections with concrete evidence
5. Collect missing mandatory fields; ask for recommended ones in one batched message
6. Research npmjs.com for target search terms; propose `description`, `keywords`, and README wording for better discoverability
7. Generate or update `README.md`
8. Post-generation review — validate completeness, check all sections and API entries
9. Quality assurance — typo detection, capitalization, punctuation, and formatting polish

## Customizing

Edit `README-template.md` to adjust structure or add sections. Edit `SKILL.md` to change detection signals, prompts, or generation rules. The skill uses your local copies automatically.
