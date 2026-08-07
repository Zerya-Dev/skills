# Repository guidelines

This repository publishes reusable agent skills through skills.sh.

## Scope

- Store every public skill in `skills/<technology>/<skill-name>/`.
- Group skills by their primary technology, such as `dotnet`, `cloudflare`, or `typescript`.
- Use lowercase kebab-case for both directories and the frontmatter `name`.
- Make the `<skill-name>` directory match the frontmatter `name` exactly.
- Keep one clear capability per skill.
- Do not place repository-level documentation inside a skill directory.

## Required skill format

Every skill must contain a `SKILL.md` beginning with YAML frontmatter that has exactly the
information agents need to discover it:

```yaml
---
name: skill-name
description: Describe both the capability and the situations that should trigger it.
---
```

Write the body as concise, imperative instructions. Put optional reusable content in:

- `scripts/` for executable, deterministic helpers;
- `references/` for documentation loaded only when needed;
- `assets/` for templates and files used in generated output;
- `agents/openai.yaml` for optional OpenAI UI metadata.

Avoid extra files such as per-skill READMEs, changelogs, or installation guides. Link directly
from `SKILL.md` to any supporting reference the agent may need.

## Validation

Before submitting changes, confirm that every skill uses exactly the
`skills/<technology>/<skill-name>/SKILL.md` layout, that `<skill-name>` matches its frontmatter
`name`, and that every description explains when the skill should activate. GitHub Actions runs
the same structural checks for pushes and pull requests.

Do not commit secrets, credentials, generated caches, or dependencies. Test every executable
helper added under `scripts/`.
