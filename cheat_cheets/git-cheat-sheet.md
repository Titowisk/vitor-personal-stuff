# To search the commit log (across all branches) for the given text:

`git log --all --grep='Build 0051'`

# To search the actual content of commits through a repo's history, use:

`git grep 'Build 0051' $(git rev-list --all)`
to show all instances of the given text, the containing file name, and the commit sha1.

Finally, as a last resort in case your commit is dangling and not connected to history at all, you can search the reflog itself with the -g flag (short for --walk-reflogs:

git log -g --grep='Build 0051'
EDIT: if you seem to have lost your history, check the reflog as your safety net. Look for Build 0051 in one of the commits listed by

git reflog

# Para obter todas as mudanças do repositório remoto (App service do Azure)
`git fetch --all`

# Para escolher somente mudanças oriundas do repositório remoto
`git reset --hard origin/master`

# Realizar cherry pick alterando o commit
`git cherry-pick <commit-hash> --edit`
- adicionar na linha principal: `cherry-picked`
- adicionar na última linha: `cherry picked from commit: <commit-hash>`

# Verificar a diferença de commits entre a branch local e a branch remota
Em outras palavras: unpushed commits :D
`git log origin/master..HEAD --oneline`

# Renomeiar branch local já lançada remotamente (already pushed)
Renomeiar: `git branch -m "<new_name>"`
Upload da branch nova: `git push origin -u <new_name>`
Deleção da antiga: `git push origin --delete <old_name>`

# Aborta processo de merge

# Tag the commit with this command:
`git tag -a M1 e3afd034 -m "Tag Message"`

# Desfaz último commit feito que não foi feito "push"
`git reset --soft HEAD~1`

# Custom Format Log
`git log --pretty=format:"%h%d%x09%an%x09%ad%x09%s" --date=short -20`
`git log master..staging --pretty=format:"%h%x09%an%x09%ad%x09%s" --date=short`

# Lists all the remote branches.
git branch -r

# Update remote branches 
`git remote -v update`
-v: show which remote had updates

# Push commit and tags at the same time
`git push --atomic origin <branch name> <tag>`

git push --atomic origin master v0.5.1

# Rename local branch not yet pushed
`git branch -m <new_name>`

# Stash uncommitted changes
Temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later on. Stashing is handy if you need to quickly switch context and work on something else, but you're mid-way through a code change and aren't ready to commit.

## Stash your current changes
`git stash`
or with a message:
`git stash save "your descriptive message"`

## List your stashes
`git stash list`

## Re-apply stashed changes
To re-apply the most recently stashed changes and remove them from the stash list:
`git stash pop`

To re-apply a specific stash (e.g., `stash@{0}`) without removing it from the stash list:
`git stash apply stash@{0}`

## View stash diff
To see the diff of a stash (e.g., the most recent one):
`git stash show -p`
For a specific stash:
`git stash show -p stash@{1}`

## Delete stashes
To delete a specific stash (e.g., `stash@{0}`):
`git stash drop stash@{0}`

To delete all stashes:
`git stash clear`