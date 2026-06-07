# writing

A Claude Code plugin marketplace of writing and content-creation skills, built on the open Agent Skills standard - so the same skills work in Claude Code, GitHub Copilot CLI, and other compatible agents.

## Skills

- **changelog-generator** - turn git commit history into user-facing changelogs.
- **content-research-writer** - research-backed content writing with citations and iterative feedback.
- **humanizer** - remove signs of AI-generated writing from text.

## Install in Claude Code (plugin)

```
/plugin marketplace add corticalstack/writing
/plugin install writing@writing
```

Restart Claude Code. The skills then appear in autocomplete as `/writing:<name>` (e.g. `/writing:humanizer`).

## Install in GitHub Copilot CLI (and other agents)

The skills live in `.claude/skills/`, which Copilot CLI reads. Install them with the `gh skill` command (GitHub CLI **v2.90.0+**):

```
# all skills from this repo, available everywhere (user scope):
gh skill install corticalstack/writing --agent github-copilot --scope user

# or just one skill:
gh skill install corticalstack/writing humanizer --agent github-copilot --scope user
```

In a Copilot CLI session: reload with `/skills reload`, confirm with `/skills info humanizer`, then invoke by name, e.g. *"Use the /humanizer skill to clean up this text."*

`gh skill` also accepts `--agent claude-code`, `cursor`, `gemini-cli`, `codex`, and many others, so the same repo serves any Agent-Skills-compatible tool. (Alternatively, copy a skill's folder into your project `.claude/skills/` or personal `~/.copilot/skills/` and run `/skills reload`.)

## Licensing

Repository scaffolding (manifests, README) is MIT. Individual skills retain their own licenses where present: `humanizer` is MIT; `changelog-generator` and `content-research-writer` carry no explicit license - treat them as the maintainer's own.
