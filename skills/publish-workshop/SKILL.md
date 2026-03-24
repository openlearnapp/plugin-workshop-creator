---
name: publish-workshop
description: Publish a finished workshop to GitHub and deploy it live on open-learn.app via GitHub Pages
argument-hint: [workshop-name]
allowed-tools: Read, Write, Bash, Glob, Grep
---

# Workshop Publisher

Veröffentliche einen fertigen Workshop aus `/Users/reza/Github/openlearnapp/workshops/` auf GitHub Pages.

## Ablauf

1. Lies `/Users/reza/Github/openlearnapp/workshops/workshop-status.md`
2. Finde Workshops mit Status `BEREIT ZUM IMPORTIEREN`
3. Falls ein Argument übergeben wurde, importiere nur diesen Workshop

## Für jeden Workshop:

### a) GitHub-Repo prüfen/erstellen
```bash
gh repo view openlearnapp/workshop-{name} 2>/dev/null
# Falls nicht gefunden:
gh repo create openlearnapp/workshop-{name} --public --description "Open Learn Workshop: ..."
```

### b) GitHub Pages Workflow erstellen
Erstelle `.github/workflows/static.yml` im Workshop-Ordner **vor** dem ersten Commit:
```yaml
name: Deploy to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### c) Dateien committen und pushen
```bash
cd /Users/reza/Github/openlearnapp/workshops/workshop-{name}
git init && git remote add origin https://github.com/openlearnapp/workshop-{name}.git
git add -A && git commit -m "feat: {name} workshop — {N} lessons, {languages}"
git branch -M main && git push -u origin main
```

### d) GitHub Pages aktivieren
```bash
gh api repos/openlearnapp/workshop-{name}/pages -X POST -f build_type=workflow 2>/dev/null || true
```

### e) Default-Sources aktualisieren
Prüfe ob die URL bereits in `public/default-sources.yaml` steht:
```yaml
- https://openlearnapp.github.io/workshop-{name}/index.yaml
```
Falls nicht: hinzufügen, committen, PR erstellen.

### f) Status aktualisieren
In `workshop-status.md`: Status → `✅ Im Projekt`

## Regeln
- NIEMALS ohne Reza's OK mergen
- Immer zeigen was gemacht wird
- GitHub Pages braucht 1-2 Minuten nach Push
