# Git LFS

Cat Snake uses [Git LFS](https://git-lfs.com/) for large binary assets. Tracking rules live in `.gitattributes` at the repo root.

## Why we use it

Git stores every version of every file inside the repository. That works well for code and text, but poorly for Unity assets: models, textures, audio, GIFs, PDFs, and large scenes pile up quickly.

We adopted LFS after pushes started failing on commits with lots of media (Wiki captures, GLBs, MP3s, large `.unity` scenes). LFS keeps tiny pointer files in Git and stores the real binaries in separate LFS storage (GitHub LFS).

## Mental model

| | Regular Git | Git LFS |
|---|---|---|
| Good for | Code, markdown, small text files | Models, textures, audio, video |
| Bad for | Large binaries piling up in history | Simplicity — extra setup required |
| On disk in a clone | Full file contents | Pointer file until `git lfs pull` / checkout |

## What is tracked

Current patterns in `.gitattributes`:

- Models: `*.glb`, `*.gltf`, `*.fbx`
- Images: `*.png`, `*.jpg`, `*.jpeg`, `*.webp`, `*.gif`, `*.psd`
- Audio: `*.mp3`, `*.wav`, `*.ogg`
- Docs/media: `*.pdf`, `*.mp4`, `*.mov`
- Unity: `*.unity`, `*.unitypackage`
- Other: `*.dll`

Only commits after the LFS setup (June 2026) were migrated. Older history may still contain some large files as regular Git blobs.

## Setup (new clone or new machine)

```bash
git lfs install
git clone <repo-url>
cd Cat\ Snake
git lfs pull   # usually automatic via hooks, but run if assets look broken
```

Close Unity before pulling if possible, then reopen the project after LFS files are on disk.

## Common pitfall: pointer files

LFS pointer files are small text stubs (~130 bytes) that look like this:

```
version https://git-lfs.github.com/spec/v1
oid sha256:...
size 2421475
```

If Unity (or any tool) tries to open these as real files, you get errors like:

- `Image is too small` (DLL)
- `File could not be read` (PNG)
- `JsonParsingFailed` (GLB)
- `FSBTool ERROR: Failed decoding audio clip` (MP3)
- Broken prefabs and scenes (missing nested model imports)

**Fix:**

```bash
git lfs install
git lfs pull
# or
git lfs checkout
```

Then reimport in Unity (right-click `Assets` → Reimport, or restart the editor).

## Tradeoffs

### Benefits

- Smaller, faster Git operations on code (clone, fetch, diff)
- Pushes with large asset batches are more reliable
- Normal pattern for Unity projects with lots of binary art

### Costs

1. **Extra tooling** — LFS must be installed; teammates and CI need it too.
2. **GitHub quotas** — free tier is ~1 GB storage and ~1 GB/month bandwidth. Our migrated assets are ~54–64 MB; fine for now, but clones and CI add up.
3. **Operational friction** — pointer vs real file confusion, Unity open during pull, one-time checkout after migration.
4. **Weaker diffs** — LFS files don't show meaningful `git diff` content (scenes, textures, etc.).
5. **Partial migration** — only recent unpushed commits were converted. A full history migrate would need `git lfs migrate import --everything` and a force push.

### Alternatives we didn't choose

- Keep Wiki/design media out of the game repo (separate Obsidian vault, Drive, etc.)
- LFS only for `Assets/`, not `Wiki/`
- Remove oversized files from history instead of LFS

## Possible future tightening

- LFS: large models, PSDs, audio, maybe large textures
- **Not** LFS: code, `.meta`, small images, possibly `.unity` unless scenes are huge
- Move Wiki captures and design docs out of the game repo if LFS bandwidth becomes an issue

## Verdict

LFS was a pragmatic fix for **push failures on binary-heavy commits**, not a fundamental improvement to the game. Worth keeping while the repo keeps growing with Unity assets; revisit if the extra complexity outweighs the benefit (solo dev, rare pushes, small asset footprint).
