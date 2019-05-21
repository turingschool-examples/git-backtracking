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

