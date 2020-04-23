
## Git branch concilliation

When developing for a feature or bug that is required on only one environment, avoid using `git cherry-pick` and instead try to merge from the bottom-top, this will avoid a false unmerged branches from mismatching SHA hashes, for example:

```
git checkout release/1.0-eggplnt
git checkout -b bug/ex-1234-name 

# do work
git commit -m "EX-1234: Description"
git push

git checkout release/2.0-fennel
git checkout -b bug/ex-1234-merge-fennel
git merge bug/ex-1234-name 
git push

git checkout develop
git checkout -b bug/ex-1234-merge-develop
git merge bug/ex-1234-name 
git push

# create 3 PRs
```

    