# Git Backtracking 

So, you messed up.

It's ok. We've all been there. Thankfully, you're using Git, so going back and reverseing these changes should be easy as pie, right?

If the statement above doesn't match your VCS confidence, it's ok. That's why we're going to go over some git backtracking strategies.

## Undoing things
Let's say you're writing code and introduce a bug, or just generally making things worse. These commands are for you.

### Fixing the previous commit 
Adding the `--ammend` flag to a commit will change the most recent commit.
```bash
git commit --ammend -m "This is the correct commit message"
```

Doing this with _no staged changes_ will simply rewrite the commit message. If you want to change the contents of the most recent commit as well, you can stage any changes and run the command as well. 

```bash 
git add file_to_update.txt
git commit --ammend -m "This is the correct commit, and a file has been updated"
```

__Warning:__ Do not ammend commits that have been published to your remote. This can cause problems for any people who have begun work based off of the most recently published commit.

### Undoing local changes 

We're used to using the `checkout` command to move the `HEAD` to a different branch.
```bash
git checkout some-other-branch
```
This command moves our HEAD pointer (the version of our code / files our present working diretory contains) to the latest commit on our newly viewed branch. However, this is not the only use for checkout. We can also use it to undo changes in our working directory.
```bash
git checkout HEAD file_to_undo.txt
```
This will revert `file_to_undo.txt` to its last committed version. This allows you to selectively discard uncommited changes to specific files. 

If we want to undo all changes in the working directory to the most recent commit, we can use `git reset`

```bash
git reset HEAD
```
This will revert the HEAD back to the previous commit, while keeping our current changes in the working directrory (not staged for commit)

```bash
git reset --soft HEAD 
```
Using the `--soft` flag will reset the HEAD to the previous commit, leaving any changes in the staging area (staged and ready for commit)

```bash
git reset --hard HEAD
```
Using the `--hard` flag will reset everything to the previous commit, removing any changes from the working area. This hard reset is __unreversable__.

