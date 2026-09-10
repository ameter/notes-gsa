## First, let's see what commits are on your branch:

```
git log --oneline main..HEAD
```
## This will show commits on your branch that aren't on main. Then we can figure out how to remove the unwanted one.


ou have two commits and want to keep only the shellcheck one (d2784e6). You can do an interactive rebase:

git rebase -i main

This will open an editor showing:
pick f1bf396 fix(opencode-web): use next available port when preferred port is taken
pick d2784e6 chore(hooks): add shellcheck pre-commit hook

Change pick to drop (or just d) on the line you want to remove:
drop f1bf396 fix(opencode-web): use next available port when preferred port is taken
pick d2784e6 chore(hooks): add shellcheck pre-commit hook

Save and close the editor. The unwanted commit will be removed.