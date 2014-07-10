## Clone

git clone https://github.com/twbs/bootstrap.git

## Stage

git add .
git add file1.txt file2.js file3.html

git mv file.txt renamed.txt
git mv file.txt other/folder/

git rm idontwantyou.txt snifsnif.txt

git add --all

git reset HEAD .
git reset HEAD file1.txt file2.js file3.html

git checkout -- .
git checkout -- file1.txt file2.js file3.html

## Commit

git commit
git commit -m 'My cool and descriptive message'

## Undo

git reset <commit>
git reset HEAD~1

## Revert

git revert <commit>

## Branches

git branch
git branch -va

git checkout -b my-branch
git checkout another-branch

git branch -D my-branch

git push origin :my-branch