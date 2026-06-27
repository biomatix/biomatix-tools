---
name: example-skill
description: >
  Replace this with a precise, trigger-rich description. The description is the ONLY
  thing Claude sees when deciding whether to load the skill, so spell out what the skill
  does and the phrases a user would say to invoke it ("verify the references", "audit the
  citations", etc.). Keep the body short and move detail into references/.
---

# Example skill

## Role

State, in one or two sentences, exactly what this skill is responsible for.

## When to use

List the situations that should trigger this skill, and the adjacent skills it should
hand off to instead of doing their work.

## Invoking bundled scripts and references

A plugin skill does NOT run from its own directory — the working directory is the user's
project. Any file that ships *inside* the plugin must be addressed through the
`${CLAUDE_PLUGIN_ROOT}` environment variable, which Claude Code expands to this plugin's
root at runtime. Bundled assets are wrong if referenced as a bare relative path.

```bash
# Correct — resolves regardless of the user's working directory:
python "${CLAUDE_PLUGIN_ROOT}/skills/example-skill/scripts/do_thing.py" <input>

# Wrong — only works if the CWD happens to be the skill directory:
python scripts/do_thing.py <input>
```

Paths the *user* supplies (a `.bib` file, a manuscript, an output written next to their
input) stay relative to the working directory — do NOT prefix those with
`${CLAUDE_PLUGIN_ROOT}`. Never hard-code a machine-specific path (e.g. a personal library
on a `D:` drive); take it as an argument or read it from an environment variable so the
plugin is portable.

## Workflow

1. Step one.
2. Step two.

## Bundled files

- `scripts/` — executables that ship with the skill (address via `${CLAUDE_PLUGIN_ROOT}`).
- `references/` — supporting docs Claude reads on demand (address via `${CLAUDE_PLUGIN_ROOT}`).

## Dependencies

List any runtime requirements (e.g. "Python 3 with `requests`").
