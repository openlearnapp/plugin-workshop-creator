# Workshop Creator Plugin for Claude Code

Claude Code Plugin for the [OpenLearn Platform](https://open-learn.app) — generates complete YAML workshop content in multiple languages.

## Skills

| Skill | Command | Description |
|-------|---------|-------------|
| **workshop-creator** | `/workshop-creator <topic>` | Generate a complete workshop with lessons, assessments, and thumbnails |
| **import-workshop** | `/import-workshop [name]` | Import a finished workshop into GitHub and make it live |
| **check-workshops** | `/check-workshops` | Show status of all workshops (local, online, sources) |

## Installation

In Claude Code, install the plugin:
```
/install-plugin https://github.com/openlearnapp/plugin-workshop-creator
```

Or clone into your project:
```bash
git clone https://github.com/openlearnapp/plugin-workshop-creator .claude/plugins/workshop-creator
```

## What it generates

A complete workshop with:
- 10-12 structured lessons per topic
- 4 interface languages (German, English, Farsi, Arabic)
- Interactive assessments (input, select, multiple-choice)
- SVG thumbnails
- Terminal simulators for IT/code workshops
- Learning methods: Active Recall, Spaced Repetition, Desirable Difficulty

## Workshop Structure

```
workshop-{topic}/
├── index.yaml          # Available languages
├── index.html          # Redirect to open-learn.app
├── deutsch/
│   ├── workshops.yaml  # Metadata + colors
│   └── {topic}/
│       ├── lessons.yaml
│       ├── thumbnail.svg
│       └── 01-{title}/content.yaml
├── english/
├── farsi/
└── arabic/
```

## License

MIT
