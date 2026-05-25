# Salma Amr — CV

ATS-friendly LaTeX CV for [Salma Amr](https://github.com/salmaamr129),
Backend Developer.

The current build of the CV is committed to this branch as
[`Salma_Amr_CV.pdf`](Salma_Amr_CV.pdf) — GitHub renders it natively in
your browser. No need to compile locally just to read it.

## What's in here

```
main.tex                 LaTeX preamble + header (name, contact, GitHub, LinkedIn)
sections/                One file per CV section
  summary.tex            Professional Summary
  experience.tex         Raya internship + DEPI program
  education.tex          MUST, BSc Computer Science
  projects.tex           5 projects with GitHub links
  skills.tex             Technical Skills
  certifications.tex
  organizations.tex
  languages.tex
Salma_Amr_CV.pdf         Auto-generated on every push by CI

.github/workflows/       CI: builds the PDF, commits it back, attaches to releases
.latexmkrc               Build config (used locally + on Overleaf)
.devcontainer/           Docker dev environment (one-click reproducible builds)
.vscode/                 Editor settings + recommended extensions
```

## How do I edit and rebuild the CV?

See **[PLAYBOOK.md](PLAYBOOK.md)** — three ways to build:

- **Overleaf** (no install, browser only)
- **Devcontainer** (Docker + VS Code, no host TeX setup)
- **Native** (TeX Live on your machine, fastest builds)

In all three paths CI will rebuild and re-commit `Salma_Amr_CV.pdf`
automatically the next time the repo is pushed.

## Customize

- **Header (name, contact, LinkedIn, GitHub)** — top of `main.tex`.
- **A specific section** — edit `sections/<name>.tex`.
- **Add a new section** — drop `sections/foo.tex`, add
  `\input{sections/foo}` near the bottom of `main.tex` where the
  other sections are included.

## Built with

Scaffolded from the [`latex-kit`](https://github.com/MohammedEl-sayedAhmed/latex-kit)
template (same repo compiles locally, in a devcontainer, and on
Overleaf — zero config).
