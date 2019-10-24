Git
===
---

# branch 削除

## remote branch

```bash
# remote branch
git push --delete [reomte repository] [branch name]
git push --delete origin tex
# local branch
git branch --delete [branch name]
git branch --delete tex
```

## pull specific remote branch

```bash
# NG
git pull [remote repository] [remote branch]:[new local branch]
git pull origin new-branch:new-branch
# OK
git branch [new local branch] [remote repository]/[branch]
git branch tex2 origin/tex
```

## merge
```bash
# move to branch which will be left
git checkout [branch]
# merge
git merge [merged branch]
# ERROR "fatal: refusing to merge unrelated histories"
git merge [merged branch] --allow-unrelated-histories
```
