# biomatix-tools

A [Claude Code](https://claude.com/claude-code) **plugin marketplace** for Biomatix.
It packages Biomatix's internal skills, slash commands, and agents — conservation-genetics
report writing, reference/citation verification, literature search, and related workflows —
so they can be installed into any Claude Code session with a single command.

## Available plugins

| Plugin | Description |
|---|---|
| `clear-writing` | Writing collaborator applying rigorous prose principles (Strunk; Gastel & Day) to draft, review, revise, or improve any text. |

Add more by following [Adding a plugin](#adding-a-plugin) below.

## Repository layout

```
biomatix-tools/
├── .claude-plugin/
│   └── marketplace.json     # Marketplace manifest — lists every published plugin
├── plugins/                 # One directory per published plugin
│   └── clear-writing/       #   prose-principles writing collaborator
├── template/                # Copy-ready plugin scaffold (not itself a published plugin)
│   ├── .claude-plugin/
│   │   └── plugin.json
│   └── skills/
│       └── example-skill/
│           ├── SKILL.md
│           ├── scripts/
│           └── references/
├── .gitignore
├── LICENSE                  # MIT
└── README.md
```

The `template/` directory is intentionally **not** registered in `marketplace.json`, so it
will never be installed. It exists only as a starting point to copy from.

## Installing this marketplace (for users)

In any Claude Code session:

```
/plugin marketplace add biomatix/biomatix-tools
```

Then browse and install individual plugins:

```
/plugin install <plugin-name>@biomatix-tools
```

For example:

```
/plugin install clear-writing@biomatix-tools
```

## Adding a plugin

1. **Copy the template** into `plugins/` under the new plugin's name:

   ```bash
   cp -r template plugins/<plugin-name>
   ```

2. **Edit the manifest** `plugins/<plugin-name>/.claude-plugin/plugin.json` — set `name`,
   `description`, and `version`.

3. **Add your content.** Replace the `example-skill` directory with one or more real
   skills (each a `skills/<skill-name>/SKILL.md`, plus optional `scripts/` and
   `references/`). You can also add `commands/`, `agents/`, hooks, or an `.mcp.json`.

4. **Fix bundled paths.** A plugin skill runs with the *user's* project as the working
   directory, **not** the skill's own folder. Any file shipped inside the plugin must be
   referenced through the `${CLAUDE_PLUGIN_ROOT}` variable, which Claude Code expands to
   the plugin root at runtime:

   ```bash
   # Correct:
   python "${CLAUDE_PLUGIN_ROOT}/skills/<skill-name>/scripts/do_thing.py" <input>
   # Wrong (bare relative path — breaks unless CWD is the skill dir):
   python scripts/do_thing.py <input>
   ```

   Leave paths the *user* supplies (their `.bib`, manuscript, outputs) relative to the
   working directory. Never hard-code machine-specific paths (e.g. a personal library on a
   `D:` drive) — take them as arguments or environment variables so the plugin is portable.

5. **Register the plugin** by adding an entry to the `plugins` array in
   `.claude-plugin/marketplace.json`:

   ```json
   {
     "name": "<plugin-name>",
     "description": "What it does and when to use it.",
     "source": "./plugins/<plugin-name>",
     "category": "development",
     "author": { "name": "Biomatix", "email": "arthur.georges@biomatix.com.au" }
   }
   ```

6. **Validate** by adding this local checkout as a marketplace and installing the plugin:

   ```
   /plugin marketplace add /absolute/path/to/biomatix-tools
   /plugin install <plugin-name>@biomatix-tools
   ```

7. **Commit and push** (see below).

## Manifest reference

**`marketplace.json`** — one per repository:

| Field | Purpose |
|---|---|
| `name` | Marketplace id users type after `@` (here: `biomatix-tools`). |
| `description` | Shown in the `/plugin` catalogue. |
| `owner` | Marketplace maintainer. |
| `plugins[]` | Array of plugin entries. `source` is `./plugins/<name>` for plugins in this repo, or a git/url source for external ones. |

**`plugin.json`** — one per plugin, in `<plugin>/.claude-plugin/`:

| Field | Purpose |
|---|---|
| `name` | Plugin id (used as `<name>@biomatix-tools`). |
| `description` | What it does / when to use it. |
| `version` | Semver string. |
| `author` | Plugin author. |

## Publishing to GitHub

This scaffold was staged outside a git repository. To publish it as
`github.com/biomatix/biomatix-tools`, run from the repository root:

```bash
git init
git add -A
git commit -m "Initial commit: biomatix-tools marketplace scaffold"
git branch -M main

# HTTPS remote:
git remote add origin https://github.com/biomatix/biomatix-tools.git
# — or SSH remote (use one, not both):
# git remote add origin git@github.com:biomatix/biomatix-tools.git

git push -u origin main
```

Create the empty `biomatix/biomatix-tools` repository on GitHub first (no README/license,
to avoid a merge conflict on the first push).

## License

[MIT](LICENSE) © 2026 Biomatix
