### Cloning, pulling, and pushing[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#cloning-pulling-and-pushing "Direct link to Cloning, pulling, and pushing")

|                    | Git                                       |
| ------------------ | ----------------------------------------- |
| Clone              | `git clone http://github.com/foo my_repo` |
| Pull               | `git fetch`                               |
| Pull a branch      | `git fetch origin REFSPEC`                |
| Pull and rebase    | `git pull --rebase`                       |
| Push to a branch   | `git push HEAD:BRANCH`                    |
| Add a remote       | `git remote add REMOTE URL`               |
| Pull from a remote | `git fetch REMOTE`                        |

Sapling [only](https://sapling-scm.com/docs/introduction/differences-git#sapling-may-not-download-all-the-repository-data-during-clonepull) clones and pulls a subset of remote branches.

### Understanding the repository[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#understanding-the-repository "Direct link to Understanding the repository")

|                 | Git                  |
| --------------- | -------------------- |
| Your commits    | N/A                  |
| Current history | `git log`            |
| Edited files    | `git status`         |
| Current hash    | `git rev-parse HEAD` |
| Pending changes | `git diff`           |
| Current commit  | `git show`           |

### Referring to commits[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#referring-to-commits "Direct link to Referring to commits")

|                               | Git     |
| ----------------------------- | ------- |
| Current commit                | `HEAD`  |
| Parent commit                 | `HEAD^` |
| All local commits             | `N/A`   |
| Commits in branch X but not Y | `Y..X`  |

See `sl help revset` for more ways of referencing commits.

### Working with files[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#working-with-files "Direct link to Working with files")

|                        | Git                           |
| ---------------------- | ----------------------------- |
| Add new file           | `git add FILE`                |
| Un-add new File        | `git rm --cached FILE`        |
| Remove file            | `git rm FILE`                 |
| Rename file            | `git mv OLD NEW`              |
| Copy file              | `cp OLD NEW`                  |
| Add/remove all files   | `git add -A .`                |
| Undo changes           | `git checkout -- FILE`        |
| Undo all changes       | `git reset --hard`            |
| Delete untracked files | `git clean -f`                |
| Output file content    | `git cat-file -p COMMIT:FILE` |
| Show blame             | `git blame FILE`              |

### Working with commits[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#working-with-commits "Direct link to Working with commits")

|                       | Git                                  |
| --------------------- | ------------------------------------ |
| Commit changes        | `git commit -a`                      |
| Modify commit         | `git commit -a --amend`              |
| Move to commit        | `git checkout COMMIT`                |
| Remove current commit | `git reset --hard HEAD`^             |
| Edit message          | `git commit --amend`                 |
| Rebase commits        | `git rebase main`                    |
| Complex rebase        | `git rebase --onto DEST BOTTOM^ TOP` |
| Rebase all            | N/A                                  |
| Interactive rebase    | `git rebase -i`                      |
| Interactive commit    | `git add -p`                         |
| Cherry-pick           | `git cherry-pick COMMIT`             |
| Stash changes         | `git stash`                          |
| Unstash changes       | `git stash pop`                      |

### Undo, redo, and reverting[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#undo-redo-and-reverting "Direct link to Undo, redo, and reverting")

|                              | Git                           |
| ---------------------------- | ----------------------------- |
| Undo commit                  | `git reset --soft HEAD^`      |
| Undo partial commit          | `git reset --soft HEAD^ FILE` |
| Undo amend                   | `git reset HEAD@{1}`          |
| Undo rebase/etc              | `git reset --hard HEAD@{1}`   |
| Revert already landed commit | `git revert COMMIT`           |
| View recent commits          | `git reflog`                  |
| Recover commit               | `git reset COMMIT`            |

### Working with stacks[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#working-with-stacks "Direct link to Working with stacks")

|                         | Git                                            |
| ----------------------- | ---------------------------------------------- |
| Modify middle commit    | `git rebase -i`                                |
| Move up/down the stack  | `git rebase -i`                                |
| Squash last two commits | `git reset --soft HEAD^ && git commit --amend` |
| Split a commit into two | `N/A`                                          |
| Reorder commits         | `git rebase -i`                                |
| Amend down into stack   | `N/A`                                          |

### Giving commits names[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#giving-commits-names "Direct link to Giving commits names")

|                        | Git                  |
| ---------------------- | -------------------- |
| Listing branches       | `git branch`         |
| Create branch/bookmark | `git branch NAME`    |
| Switch to branch       | `git checkout NAME`  |
| Delete a branch        | `git branch -d NAME` |

### Resolving conflicts[​](https://sapling-scm.com/docs/introduction/git-cheat-sheet/#resolving-conflicts "Direct link to Resolving conflicts")

|                           | Git                                    |
| ------------------------- | -------------------------------------- |
| List unresolved conflicts | `git diff --name-only --diff-filter=U` |
| Mark a file resolved      | `git add FILE`                         |
|                           |                                        |