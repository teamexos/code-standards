# Development Tips and Tricks
This document contains a collection of useful tips and tricks for development (and debugging).

## I. Debugging

Read the callstack carefully. Dont rush to google the last error reported. Understand where the error is originating. Is it in our app or a dependency or a dependency of a dependency?

### Layout Issues

Less is more. Delete elements from the DOM to see at what point the layout issue is fixed. Then you know which component/element is causing the layout issue.

## II. Local development

To develop for any of the instances using the local server but not the local docker installation, the verbose version of the serve command is available in the README file in most projects (e.g. `APP_ENV=detect yarn serve --host=exos-qa.exos.biz --https --port 3000`). This will adjust the client's API services run the application in https://exos-qa.exos.biz:3000. 

### Node version and packages

If a project has an `.nvmrc` file, make sure you use `nvm use` when opening it, since it usually indicates that it requires a specific node version, and compilation could fail if that specific version is used. 

## III. Git Solutions

### Changing a Commit Message
You can change a commit message or add more changes to a commit before pushing by using `--amend`.
```
git commit --amend -m "[Commit Message]"
```
### Removing a Commit Before Pushing
You can remove a file from staging, before a commit
```
git reset HEAD <file>
```
To remove changes from a commit before pushing, you can work backwards from the current commit.
```
git reset HEAD~1
```
Or you can move backward to a specific commit.
```
git reset <commit hash>
```
When using `reset`, you can specify the level of resetting you wan to accomplish.
- `--soft` Moves the commit back into the staging area
- `--mixed` Unstages the commit
- `--hard` Unstages the commit and removes the change altogether

### Undoing a Commit
YOu can undo a commit that has already been pushed, by using `revert`.  It will not remove the undone commit from the history however.
```
git revert <commit hash>
```

### Moving a Commit from One Branch to Another
Sometimes when working on multiple tasks simultaneously, you may lose track of the branch you're on and commit a change to the incorrect branch.  In those situations, you can use a combination of `cherry-pick`, `reset` and `checkout` to move the commit.

First, switch to the branch (`git checkout <branch>`) you want to move the commit, find the hash for the commit you want to move (`git log --oneline`) and use `cherry-pick` to add that commit to the target branch.
```
git cherry-pick <commit hash>
```

Then switch to the branch you want to remove the commit from (`git checkout <branch>`) and perform a `reset` on the commit hash.
```
git reset <commit hash>
```

And to get the file back to it's previous state, use `checkout` on the file.
```
git checkout <file>
```
