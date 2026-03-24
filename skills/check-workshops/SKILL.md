---
name: check-workshops
description: Show the current status of all workshops — local files, GitHub repos, default sources, and what needs attention
allowed-tools: Read, Bash, Glob, Grep
---

# Workshop Status Check

Zeige den vollständigen Status aller Workshops.

## Ablauf

1. Lies `/Users/reza/Github/openlearnapp/workshops/workshop-status.md`
2. Liste alle lokalen Workshop-Ordner: `ls /Users/reza/Github/openlearnapp/workshops/workshop-*/`
3. Prüfe GitHub-Repos: `gh repo list openlearnapp --limit 30 | grep workshop`
4. Prüfe `public/default-sources.yaml`
5. Für jeden Workshop zeige:

```
| Workshop | Lokal | Online | Sprachen | Default-Source | Status |
|----------|-------|--------|----------|----------------|--------|
| linux    | 12    | 12     | de,en,fa,ar | ✅          | ✅ Live |
| farsi    | 10    | 10     | de,en,fa    | ✅          | ✅ Live |
```

6. Zeige offene Aufgaben aus `📤 Aufgaben` und `🚨 BUG`-Bereiche
7. Zeige Qualitätsprobleme aus `📋 QUALITÄTSBERICHT`
