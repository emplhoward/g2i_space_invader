# Run Space Attack

Open `index.html` directly in a browser. No install, server, or build is needed.
On this Linux machine:

```bash
cd /home/leo/Documents/github/g2i_space_invader
xdg-open index.html
```

Press Enter for wave 1, or select Start Medium / Start Hard. Use arrows or WASD
to move, hold Space to fire, and press P or Escape to pause. See
`architecture.md` for gameplay details and verification steps.

## Pull

Bring in remote changes when your working tree is clean (commit your work first):

```bash
git pull --ff-only
```

If Git reports divergent branches, stop and inspect them before choosing how
to integrate the changes.

## Commit

Review and save your local changes:

```bash
git status
git diff
git add -A
git diff --cached
git commit -m "routine"
```

`git add -A` includes all additions, edits, and deletions in the repository.
Review the staged diff before committing.

## Push

Send committed changes to the configured upstream:

```bash
git push
```

If the current branch has no upstream yet:

```bash
git push -u origin HEAD
```

Pull and push require a configured remote and access to it. Check remotes with
`git remote -v`. Creating the game does not commit or push it automatically.
