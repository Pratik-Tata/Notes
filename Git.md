
---

# 📝 Git & GitHub Reference Notes

## 1. Basics of Git Repositories

- **Distributed system** → Every clone (your laptop, GitHub, teammate’s machine) is a _full copy_ of the repo (all commits, branches, tags).
    
- **Branches** → Just pointers (labels) to commits in the DAG (directed acyclic graph).
    
- **Commits** → Snapshots of the whole project at a point in time.
    

---

## 2. `origin` and Remotes

- **`origin`** → default nickname for the remote repo you cloned from.
    
    - Example: `git@github.com:user/repo.git`.
        
- **Remote-tracking branches** (e.g. `origin/main`, `origin/feature-x`) → read-only local pointers to the remote’s state.
    
- Local branches can “track” a remote branch, creating an **upstream relationship**.
    

Check tracking info:

```bash
git branch -vv
```

---

## 3. `checkout` vs `switch` vs `restore`

- **`git checkout`** (old command, overloaded):
    
    - Switch branches.
        
    - Create new branch (`git checkout -b feature-x`).
        
    - Restore files from commits (`git checkout <commit> -- file`).
        
- **Modern replacements**:
    
    - `git switch` → branch work.
        
        - `git switch feature-x` → switch branch.
            
        - `git switch -c feature-x` → create + switch.
            
    - `git restore` → file-level restores.
        
        - `git restore file.txt` → reset file to last commit.
            
        - `git restore --source=abc123 file.txt` → reset to specific commit.
            

---

## 4. `add` vs `commit`

- `git add` → stages changes (like putting groceries in the cart).
    
- `git commit` → finalizes a new snapshot in history (checkout at register).
    
- Typical workflow:
    
    ```bash
    git add .
    git commit -m "message"
    git push
    ```
    

---

## 5. Fetch vs Pull

- **`git fetch`**: updates your local remote-tracking branches (`origin/main`, etc.) but does _not_ touch your current branch/files.
    
- **`git pull`** = `git fetch` + merge (or rebase). Immediately integrates new commits into your branch.
    

👉 Use `fetch` for a “safe peek” at what’s new, `pull` when you want to actually update your branch.

---

## 6. Merging Branches

- Rule of thumb: **switch to the branch you want to update**, then merge the other in.  
    Example: to bring `A` into `B`:
    
    ```bash
    git switch B
    git merge A
    git push origin B
    ```
    
- **Merge** → preserves history, creates merge commit (unless fast-forward).
    
- **Rebase** → rewrites history, replays your commits on top of another branch for linear history.
    

---

## 7. Rebasing

- Operates on **commits**, not branches.
    
- Collects commits from your branch, moves HEAD to new base, and replays your commits one by one.
    
- Conflict resolution happens **per commit**.
    
- Exit options:
    
    - `git rebase --continue` → after resolving conflicts.
        
    - `git rebase --skip` → drop the current commit.
        
    - `git rebase --abort` → go back to pre-rebase state.
        
    - `git rebase --quit` → stop, but keep current partial state.
        

---

## 8. Merge Conflicts

- Happen when **two commits modify the same lines in a file**.
    
- Conflict markers:
    
    ```text
    <<<<<<< HEAD
    your local version
    =======
    remote version
    >>>>>>> branch-or-commit
    ```
    
- Resolution: manually edit, then:
    
    ```bash
    git add conflicted-file
    git rebase --continue   # or git merge --continue
    ```
    

---

## 9. Local vs Remote Sync

- **If remote has new commits** → `pull` brings them into local.
    
- **If local has new commits** → `push` sends them to remote.
    
- **If both diverged** → `pull` results in a merge or rebase.
    
- **If you have uncommitted edits** → `pull` aborts with:
    
    ```
    error: Your local changes ... would be overwritten by merge
    ```
    
    Git will never overwrite local uncommitted changes without explicit reset/restore.
    

---

## 10. Reset vs Restore vs Stash

- **Restore** = discard local _uncommitted_ changes (file-level or whole repo).
    
- **Reset --hard** = reset entire branch (destroy local commits + changes).
    
- **Stash** = temporary save of local edits, so you can pull/rebase safely and then reapply:
    
    ```bash
    git stash -u
    git pull --rebase
    git stash pop
    ```
    

---

## 11. Upstream vs Downstream

- **Upstream** = the remote branch your local branch is tracking (e.g., `origin/feature`).
    
- **Downstream** = your local branch (informal term).
    
- Ahead/behind is always measured relative to upstream.
    

---

## 12. Worktrees

- `git worktree add ../dir branch` → create a new working directory for a different branch, without switching in your current repo.
    
- Useful to work on two branches simultaneously.
    

---

## 13. GitHub Specifics

- **Commits from UI**: If you edit a file on GitHub, it creates a commit in remote → local won’t see it until `fetch`/`pull`.
    
- GitHub PR “Merge” button → creates a merge commit in remote. If you merged differently locally, histories may differ.
    
- GitHub always compares **commits**, not “files.” That’s why ahead/behind is purely DAG math.
    

---

## 14. Safety Net

- Git will **never overwrite uncommitted changes** on pull/merge. It aborts instead.
    
- Only explicit destructive commands (`git reset --hard`, `git restore`) overwrite your edits.
    

---

# ✅ Mental Models to Keep

- `pull` = “bring news + apply it”
    
- `fetch` = “just bring news”
    
- `merge` = “combine histories, keep both versions intact”
    
- `rebase` = “replay my work on top of theirs, make it look linear”
    
- `origin` = “nickname for remote repo”
    
- Commits = real truth, branches = just labels
    

---

