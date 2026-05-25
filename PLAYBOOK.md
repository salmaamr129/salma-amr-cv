# Playbook — Build the CV

Step-by-step guide for editing the CV and producing
`Salma_Amr_CV.pdf`. Three paths — pick whichever fits your machine
best. All three produce a byte-identical PDF.

If you just want to **read** the current CV, open
[`Salma_Amr_CV.pdf`](Salma_Amr_CV.pdf) directly on GitHub — it renders
in the browser. You only need any of the paths below if you want to
**edit** the source and rebuild.

---

## Path A — Overleaf (browser, no install)

The fastest way to get going if you've never used LaTeX locally.

1. Make a zip of the repo (skipping git internals and editor folders):
   ```bash
   zip -r project.zip . -x ".git/*" ".vscode/*" ".devcontainer/*" \
     "*.aux" "*.log" "*.fls" "*.fdb_latexmk" "*.out" "*.synctex.gz" \
     "*.toc" "*.pdf"
   ```
2. Go to https://www.overleaf.com and sign in (free account is fine).
3. **New Project → Upload Project** → drop `project.zip`.
4. Overleaf auto-detects `main.tex` and the `.latexmkrc`. Click
   **Recompile**.
5. Edit any `.tex` file in the Overleaf editor; it rebuilds on save.
6. When happy: **Menu → Download → PDF** to grab the file, OR copy any
   text changes you made back into your local clone and `git push` so
   CI rebuilds and commits the new `Salma_Amr_CV.pdf` to `main`.

---

## Path B — Devcontainer (Docker + VS Code, no host TeX setup)

For when you don't want to install TeX Live on your machine.

### One-time per machine

Install:
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [VS Code](https://code.visualstudio.com/)
- VS Code extension `ms-vscode-remote.remote-containers`

### Per session

```bash
git clone https://github.com/salmaamr129/salma-amr-cv.git
cd salma-amr-cv
code .
```

VS Code will detect `.devcontainer/devcontainer.json` and prompt
*"Reopen in Container"* — click it. The first run pulls the
`texlive/texlive:TL2025-historic` image (~1.5 GB, cached for all future
projects).

Once the container is up:

- Open `main.tex`.
- Press **Ctrl+Alt+B** to build.
- Press **Ctrl+Alt+V** to view the PDF in a side tab.
- Edit any `.tex` file — auto-build on save is enabled.

---

## Path C — Native local install (fastest builds, full offline)

For when you'll be editing the CV often and want sub-second rebuilds.

### One-time per machine (Linux)

Install TeX Live 2025-historic — the same release the devcontainer and
Overleaf use, so all three paths produce byte-identical PDFs:

```bash
HISTORIC=https://ftp.math.utah.edu/pub/tex/historic/systems/texlive/2025/tlnet-final
cd /tmp && wget "$HISTORIC/install-tl-unx.tar.gz"
tar -xzf install-tl-unx.tar.gz && cd install-tl-*/
perl ./install-tl --no-interaction --scheme=full -repository "$HISTORIC"
echo 'export PATH="$HOME/texlive/2025/bin/x86_64-linux:$PATH"' >> ~/.profile
# Log out + back in (or `source ~/.profile`).
code --install-extension James-Yu.latex-workshop
```

### Per project

```bash
git clone https://github.com/salmaamr129/salma-amr-cv.git
cd salma-amr-cv

# Build once
latexmk -pdf main.tex

# OR open in VS Code and use Ctrl+Alt+B
code .
```

---

## VS Code shortcuts (paths B & C)

| Shortcut | Action |
|---|---|
| `Ctrl+Alt+B`     | Build |
| `Ctrl+Alt+V`     | View PDF in side tab |
| `Ctrl+Alt+J`     | Jump from source → PDF (SyncTeX) |
| Double-click PDF | Jump from PDF → source |

Auto-build on save is enabled by default in `.vscode/settings.json`.

## CLI cheatsheet

```bash
latexmk -pdf main.tex     # build (default — pdflatex)
latexmk -pdf -pvc main.tex# build + auto-rebuild on save + live PDF reload
latexmk -c                # remove aux files (keep PDF)
latexmk -C                # remove everything including PDF
```

## How CI rebuilds the PDF on every push

The `Build PDF` workflow in `.github/workflows/build.yml`:

1. Runs on every push to `main` (and on PRs).
2. Compiles `main.tex` inside the `texlive/texlive:TL2025-historic`
   Docker image — same TeX Live release as paths B and C.
3. Renames `main.pdf` → `Salma_Amr_CV.pdf`.
4. Uploads it as a workflow artifact (downloadable from the Actions
   tab — the artifact is called `Salma_Amr_CV`).
5. **Commits `Salma_Amr_CV.pdf` back to `main`** using the
   `github-actions[bot]` identity, with `[skip ci]` in the message so
   the bot's own commit doesn't re-trigger the workflow.

End result: every time you push a `.tex` edit, the rendered PDF in the
repo updates on its own within ~90 seconds.

The `Release PDF` workflow fires when you push a `v*` tag
(`git tag v1.0 && git push --tags`) and attaches a renamed,
versioned PDF (`Salma_Amr_CV-v1.0.pdf`) to a GitHub Release. Useful
when you want a stable, dated link to send to recruiters.

## Overleaf-compatibility rules (keep these when adding files)

- `main.tex` stays at the project root (Overleaf needs it there).
- `\input{...}` and `\includegraphics{...}` paths are relative to the
  project root.
- Any custom `.cls` / `.sty` / fonts must live inside the repo
  (Overleaf can't see your machine).
- Anything beyond `latexmk` goes in `.latexmkrc`, not in a build
  script. Overleaf only runs `latexmk`.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `latexmk: command not found` | TeX Live not on PATH (or not installed) | Run the install commands in Path C, or use the devcontainer (Path B) |
| Overleaf can't find an `\input{sections/...}` file | Section file wasn't included in the zip | Re-zip with the `zip -r` command in Path A and re-upload to Overleaf |
| Build succeeds locally but Salma_Amr_CV.pdf isn't updated on GitHub | Pushed a commit that only modified workflow / paths-ignored files | Push any change under `sections/` or `main.tex` to re-trigger CI |
| GitHub Actions auto-commit step is skipped | Default workflow permissions on the repo are `read` | Repo Settings → Actions → General → "Workflow permissions" → choose "Read and write" |
| LaTeX error after editing | New section file declared but not referenced from `main.tex` | Add `\input{sections/<newfile>}` near the existing `\input` lines |
| VS Code LaTeX Workshop doesn't auto-build | Extension not installed | Install `James-Yu.latex-workshop` from the Extensions tab |
