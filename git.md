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

## tag

```bash
# add tag to previous commit
git tag [tag name]
# reflect tag to remote
git push origin [tag name]
git push origin --tags # all tags

# remove tag from local repository
git tag -d [tag name]
# remove tag from remote repository
git push origin :[tag name]
```

## cannot see folder at remote page
reason1 Tha file contains .git folder, so we can't stage these folder
    git says the file is submodule ....
```bash
rm /path/to/.git
git rm -rf --cache /path/to/dir
git add /path/to/dir
```

## downloat specific Folder

1. init
```bash
mkdir <repo>
cd <repo>
git init
git remote add -f origin <url>
```

1. Make git enable to download specific folder
```bash
git config core.sparseCheckout true
```

1. register folder what you want to `.git/info/sparse-checkout`

1. pull
```bash
git pull origin master
```
