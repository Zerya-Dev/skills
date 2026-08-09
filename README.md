<h1 align="center">Zerya Skills</h1>

<p align="center">
  Reusable agent skills crafted by <a href="https://github.com/Zerya-Dev">Zerya</a>.
</p>

<p align="center">
  <a href="https://skills.sh/Zerya-Dev/skills"><img alt="skills.sh installs" src="https://skills.sh/b/Zerya-Dev/skills" /></a>
  <img alt="Validation" src="https://img.shields.io/github/actions/workflow/status/Zerya-Dev/skills/validate-skills.yml?branch=master&label=skills&style=for-the-badge" />
  <img alt="License" src="https://img.shields.io/github/license/Zerya-Dev/skills?color=7c3aed&style=for-the-badge" />
  <img alt="Last commit" src="https://img.shields.io/github/last-commit/Zerya-Dev/skills?color=7c3aed&style=for-the-badge" />
</p>

**A curated collection of portable instructions that give AI coding agents focused, repeatable workflows.**

Each skill follows the open `SKILL.md` format and can be discovered and installed with the
[skills CLI](https://skills.sh/docs).

## 🚀 Installation

Browse the available skills without installing them:

```bash
npx skills add Zerya-Dev/skills --list
```

Install a selected skill:

```bash
npx skills add Zerya-Dev/skills --skill <skill-name>
```

Install every skill from this repository:

```bash
npx skills add Zerya-Dev/skills --all
```

## 🗂️ Repository structure

```text
zerya-skills/
├── .github/
│   └── workflows/
│       └── validate-skills.yml
├── skills/
│   └── <technology>/
│       └── <skill-name>/
│           ├── SKILL.md
│           ├── agents/
│           │   └── openai.yaml   # optional
│           ├── scripts/          # optional
│           ├── references/       # optional
│           └── assets/           # optional
├── AGENTS.md
├── LICENSE
└── README.md
```

Every directory directly under `skills/` groups skills by technology, for example `dotnet`,
`cloudflare`, or `typescript`. Each `skills/<technology>/<skill-name>/` directory represents one
publishable skill. Both path segments must use lowercase kebab-case, and `<skill-name>` must match
the `name` field in `SKILL.md`.

## 🛠️ Creating a skill

Create `skills/<technology>/<skill-name>/SKILL.md` with the required frontmatter. For example,
`skills/dotnet/ef-core-migrations/SKILL.md`:

```markdown
---
name: skill-name
description: Explain what the skill does and when an agent should use it.
---

# Skill name

Write concise, imperative instructions for the agent.
```

Keep detailed documentation in `references/`, deterministic helpers in `scripts/`, and output
templates or media in `assets/`. A pull request automatically validates all published skills.

## 🤝 Contributing

Read [AGENTS.md](AGENTS.md), add or update one focused skill, and open a pull request. By
contributing, you agree that your changes are distributed under the [MIT License](LICENSE).
