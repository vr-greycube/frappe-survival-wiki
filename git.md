# Git

```
# go from main to version-14 in local without fetching all remote branches
# edit .git/config > replace main with version-14

git fetch upstream version-14
git checkout version-14

# git switch branch fetch all

git remote set-branches upstream  '*'
git fetch --all --unshallow --quiet
git checkout -f version-14

# check changes before pull

git fetch
git log HEAD..upstream/main



```
