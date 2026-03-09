# npm-readme-skill

> Sophisticated AI skill for generating consistent, high-quality READMEs for npm library projects.

Produce professional READMEs for your npm packages with `npm-readme-skill` in a few minutes. Includes the following features: 

🔍 **Intelligent project analysis** — auto-discovers package metadata, repository info, CI/CD setup, test frameworks, and features  
📋 **Template-driven generation** — follows a proven structure with inline guidance for consistent quality  
✨ **Smart badge management** — evaluates badge coverage and suggests improvements  
🎯 **Optional section detection** — identifies project-specific sections that enhance the README  
♻️ **Update-safe workflow** — creates new READMEs or carefully updates existing ones while preserving custom content  
✅ **Quality validation** — comprehensive post-generation checks ensure completeness and correctness

## Quick Start

### Step 1 - Setup

For VS Code Copilot, create the skill folder and copy the files:

```bash
mkdir -p .github/skills/npm-readme-skill
cp SKILL.md README-template.md .github/skills/npm-readme-skill/
```

Your project structure should now look like:
```
my-npm-project/
├── .github/
│   └── skills/
│       └── npm-readme-skill/
│           ├── SKILL.md
│           └── README-template.md
├── src/
├── package.json
└── ...
```

For other AI tools (Claude Code, OpenCode, Cursor, etc.), drop `SKILL.md` and `README-template.md` into your tool's custom instructions folder.

### Step 2 - Generate a README

Open your AI tool's chat and ask:

```
Create a README for my npm package
```

Or:

```
Generate documentation for this npm library
```

The skill will kick in automatically and do the following:
1. Scan your project (auto-detect metadata, CI, tests, features)
2. Propose optional sections (Configuration, Setup, Changelog, etc.)
3. Ask for missing information (if needed)
4. Generate or update `README.md`
5. Validate and report success

## When to Use

- Creating the first `README.md` for an npm package
- Updating an existing README to match a professional template and quality standard
- Documenting a library for publication on npmjs.com
- Ensuring consistent README structure and styling across multiple npm packages

## Full Feature List

### Project Intelligence
- **Deep package.json parsing** — extracts name, version, license, entry points, module format, peer dependencies
- **Git repository detection** — derives GitHub user/repo from `.git/config` or `package.json`
- **Repository metadata** — analyzes CI workflows, test configuration, coverage setup
- **Module format detection** — identifies CommonJS, ESM, or dual-export packages
- **Git history analysis** — inspects version tags and commit messages for Changelog and breaking changes

### Badge Management
- **Badge coverage assessment** — evaluates License, npm version, Node version, CI status, Coverage, TypeScript, Bundle size, Downloads, Snyk
- **Coverage recommendations** — suggests GitHub Actions or Coveralls setup if badges are sparse
- **Live Coveralls probe** — checks `coveralls.io/github/{user}/{repo}` API to confirm coverage data exists
- **Alternative suggestions** — recommends no-infrastructure badges if missing tooling

### Intelligent Optional Sections
Detects and proposes:
- **Options / Configuration** — functions accepting options objects
- **Setup & Integration** — environment variables, key injection patterns
- **Naming Conventions** — `NODE_ENV`-based config files, multi-stage file loading
- **Changelog** — populated with real semver tags and `BREAKING CHANGE`/`feat!`/`fix!` commits from git log
- **Migration / Upgrading** — major version breaks and migration paths
- **SBOM** — CycloneDX software bill of materials
- **CLI / Command-line Usage** — when `bin` field is declared
- **Security** — sensitive logic, CVE handling, security policies
- **Contributing** — governance, PR/issue templates
- **Performance** — benchmarks, streaming, zero-copy design
- **License details** — for non-standard or dual-licensed projects

All optional sections can be cherry-picked by the user.

### Template-Driven Consistency
- **Proven structure** — based on analysis of real-world npm library documentation
- **Inline AI guidance** — every section contains `<!-- AI: ... -->` comments explaining generation rules
- **Formatting standards** — consistent use of badges, headings, code blocks, API signature patterns, `// Result:` comments
- **Placeholder tokens** — `{PACKAGE_NAME}`, `{GITHUB_USER}`, `{TAGLINE}`, etc. for clear substitution

### Create vs. Update Workflow
- **Create mode** — generates a complete, clean README from scratch
- **Update mode** — intelligently merges template sections into existing README:
  - Reformats outdated sections to match template style
  - Preserves custom project-specific content
  - Adds missing mandatory sections
  - Audits and improves badge rows
- **Conflict resolution** — keeps project-specific sections (Roadmap, Contributing, etc.) at the end

### Quality Assurance
- **Post-generation validation** — verifies:
  - No leftover `{PLACEHOLDER}` tokens
  - No template comment markers in final output
  - All mandatory sections present
  - Badge count ≥ 3
  - Every API function has: signature, description, Parameters, Returns (where applicable), Example with `// Result:` comments
- **Self-correction** — fixes issues before reporting to user
- **Summary report** — brief recap of what was created, sections included, deferred suggestions

### User-Centric Design
- **Reasonable defaults** — extracts as much as possible automatically before asking questions
- **Explicit warnings** — only blocks generation for truly mandatory missing data
- **Batched questions** — asks all recommended questions at once, never piecemeal
- **Non-exhaustive guidance** — signal table for optional sections is explicitly marked as a starting point; encourages thinking beyond the examples

## Files in This Skill

| File | Purpose |
|------|---------|
| **SKILL.md** | The skill definition; contains the 7-phase procedure, all generation rules, and formatting standards |
| **README-template.md** | The template for generated npm library READMEs, with inline `<!-- AI: ... -->` guidance for filling each section |

## How It Works

The skill follows a systematic 7-phase workflow to ensure complete, high-quality output. Below is a brief overview; see [SKILL.md](SKILL.md) for the full detailed procedure.

### Phase 1 — Template Review
Loads and internalizes `README-template.md`, understanding all generation rules and placeholder conventions.

### Phase 2 — Project Scan
Automatically inspects your project to extract:
- Package identity (name, version, license, entry point, module format)
- Repository & CI (GitHub user/repo, CI workflows, Coveralls status)
- Test setup (test framework, coverage tools)
- Features & optional sections (config patterns, env variables, breaking changes, security files, CLI, TypeScript, etc.)
- Existing README (flags it for update mode)

### Phase 3 — Badge Coverage Analysis
Evaluates available badges (License, npm version, Node version, CI status, Coverage, TypeScript, etc.):
- Ensures 3–5 badges minimum for quality presentation
- Proactively offers to generate GitHub Actions / Coveralls setup if sparse
- Suggests no-infra alternatives

### Phase 4 — Optional Section Proposal
Identifies project-specific optional sections with concrete evidence and requests user confirmation.

### Phase 5 — Missing Information Collection
Collects mandatory fields with explicit warning if missing. Asks for recommended fields (description, real-world use cases) in one batched message.

### Phase 6 — Generation or Update
- Create mode: generates a complete README following the template
- Update mode: merges template sections into existing README while preserving custom content

### Phase 7 — Post-generation Review
Self-validates the output and reports a summary.

## Tips for Best Results

- Ensure your `package.json` has complete metadata: `name`, `license`, `engines.node`, `repository`, `description`
- If you have a GitHub repository, make sure `.git/config` is present or `repository` field is set
- For badge coverage, consider setting up GitHub Actions and Coveralls (both free for open source)
- When the skill asks for a description expansion, provide a concrete sentence about the problem solved and intended use case
- For API-heavy packages, have your entry point source code ready so the skill can accurately analyze function signatures
- Review the generated README in context of your project's actual features — the skill generates based on patterns, not deep code analysis

## How the Template Ensures Quality

The `README-template.md` enforces consistent structure via inline `<!-- AI: ... -->` annotations:

- **Header**: badges (3–5), title, tagline blockquote
- **Description**: 1–2 prose sentences + emoji-grouped feature bullets
- **Usage**: optional motivating before/after + minimal real-world code example (CommonJS + optional ESM)
- **API Reference**: one H3 group per capability, one H4 per function, with Parameters → Returns (if applicable) → Example (`// Result:` comments)
- **Optional sections**: Configuration, Setup, Naming Conventions, Changelog, SBOM, Migration, Security, Contributing, Performance, License

Every section includes explicit guidance on what to fill in and how to style it.

## Extending the Skill

To customize the skill for your team or workspace:

1. **Modify the template** — edit `README-template.md` to adjust structure, add sections, or change formatting rules
2. **Update the procedure** — edit `SKILL.md` Phase 2, 3, 4, or 5 to add new detection signals or change prompts

The skill will automatically use your customized versions during generation.

## Related Files

- [README-template.md](README-template.md) — the generation template
- [SKILL.md](SKILL.md) — detailed procedure and rules (7 phases)
