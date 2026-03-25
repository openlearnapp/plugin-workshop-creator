# Changelog

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
