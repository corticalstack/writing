# writing

A Claude Code plugin marketplace of writing and content-creation skills.

## Skills

- **changelog-generator** - turn git commit history into user-facing changelogs.
- **content-research-writer** - research-backed content writing with citations and iterative feedback.
- **humanizer** - remove signs of AI-generated writing from text.

## Install

```
/plugin marketplace add corticalstack/writing
/plugin install writing@writing
```

Restart Claude Code; the skills then appear as `/writing:<name>` (e.g. `/writing:humanizer`).

## Licensing

The repository scaffolding (manifests, README) is MIT licensed. Individual skills retain their own licenses where present (`humanizer` is MIT; `changelog-generator` and `content-research-writer` carry no explicit license - treat as the maintainer's own).
