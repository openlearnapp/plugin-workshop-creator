# Changelog

## 1.9.1
- docs: Story Mode Navigation erklärt — Links/Rechts-Tippen springt Absatz (nicht Section), Sections haben idealerweise 2–6 Absätze, Entscheidungs-Examples ans Section-Ende

## 1.9.0
- feat: **Story Mode** — neuer Workshop-Typ für interaktive Geschichten mit Verzweigungen
- feat: `voice` Feld auf Examples — Erzähler (`narrator`) oder Figuren-Name
- feat: `goto: { lesson, section }` auf Options — Branching-Navigation bei Auswahl
- feat: `goto_correct` / `goto_wrong` auf Fragen — bedingte Weiterleitung
- feat: `answers[]` mit `goto` auf Input-Typ — mehrere akzeptierte Antworten mit individuellem Ziel
- feat: `image` auf Options — Bild pro Auswahlmöglichkeit
- feat: Workshop-Ebene `labels` in `workshops.yaml` für Genre/Zielgruppe
- docs: Story Mode Sektion in workshop-creator Skill mit vollständigem Schema
- docs: `workshop-milas-abenteuer` als Referenz-Beispiel dokumentiert
- docs: README mit Workshop-Typen Übersicht (Lern-Workshop vs. Story-Workshop)

## 1.8.0
- feat: **Section-Bilder jetzt PFLICHT** — jede Section braucht ein eigenes SVG-Bild (640×160)
- feat: **Terminal-Card-Stil** — Rahmen, macOS-Titelleiste, inhaltsspezifische Darstellung
- feat: Naming-Convention: `images/section-[nr]-[kurzname].svg` pro Section
- feat: SVG-Vorlage aktualisiert: Terminal-Card mit Farbpalette und Befehls-Hinweisen
- feat: validate-workshop prüft jetzt Section-Bilder als Fehler
- feat: update-workshop generiert fehlende Section-Bilder automatisch
- feat: extend-workshop generiert Section-Bilder für neue Lektionen
- docs: Ordnerstruktur aktualisiert (section-*.svg neben lesson-header.svg)

## 1.7.2
- fix: Assessment-Format explizit als Warnung dokumentiert — NIEMALS einfache Strings als Options (erzeugt leere Radio-Buttons)
- fix: SVG Dark-Mode-Regel: dunkler Hintergrund (#0d1117) empfohlen, kein übersetzungspflichtiger Text in Bildern
- fix: validate-workshop prüft jetzt URL-Konsistenz (open-learn.app statt openlearnapp.github.io)
- fix: validate-workshop prüft `image`-Feld in `workshops.yaml`
- fix: update-workshop ergänzt fehlende `image`-Felder in `workshops.yaml` automatisch
- fix: translate-workshop und workshop-creator markieren `image`-Feld in `workshops.yaml` als PFLICHT
- fix: Labels auch auf Assessment-Examples als Pflicht dokumentiert

## 1.7.1
- fix: make image generation instructions explicit — skills now say "write the SVG file with Write-Tool" instead of just showing a template
- fix: step-by-step instructions for each lesson: mkdir images/, Write SVG file, set image field in content.yaml

## 1.7.0
- feat: lesson images now mandatory — every lesson must have `image: "images/lesson-header.svg"` with SVG file
- feat: labels now mandatory on every example — shown as badges on lesson cards
- feat: all skills updated for new lesson overview layout (sections/examples/assessments stats, progress bars)
- feat: update-workshop generates missing lesson images and labels (idempotent)
- feat: translate-workshop copies lesson images to new language folders
- feat: extend-workshop generates images and labels for new lessons
- feat: validate-workshop checks lesson images and labels as errors (not hints)

## 1.6.1
- docs: add architecture diagram showing Claude Code, Plugin, Workshops, GitHub Pages, and Open Learn Platform
- docs: explain component connections and full authoring-to-learning cycle

## 1.6.0
- feat: add `/update-workshop` skill — idempotent updates for existing workshops (schema migration, missing README links, thumbnails, lesson images, terminal-sim, labels)
- feat: workshop-creator now generates README.md with Landing Page + Start Workshop links
- feat: workshop-creator generates SVG lesson diagrams for IT/science workshops
- feat: improved thumbnail.svg generation with SVG template and thematic symbol guide
- feat: validate-workshop checks README links, thumbnails, and media coverage
- feat: publish-workshop creates GitHub issue for Pages settings (human checklist)
- docs: updated README with new skill table, workflow diagram, and structure tree

## 1.5.0
- chore: add CLAUDE.md with workflow rules (always PRs, version bumps, changelog, docs updates)
- chore: add CHANGELOG.md with full version history
- docs: add /import-workshop to README skill table

## 1.4.0
- fix: ensure .gitignore, GitHub Actions workflow, and landing page entry for every workshop
- chore: sync marketplace.json version

## 1.3.0
- feat: add /validate-workshop skill and update plugin manifest

## 1.2.0
- feat: incremental workflow with translate, extend, and publish skills

## 1.1.0
- feat: platform alignment v2
