# Git Backtracking

So, you fucked up.

It's ok. We've all been there. Thankfully, you're using Git, so going back and reverseing these changes should be easy as pie, right?

If the statement above doesn't match your VCS confidence, it's ok. That's why we're going to go over some git backtracking strategies.

(Use the `example.md` file in the `examples` branch to test some things we're talking about)

## Undoing things
Let's say you're writing code and introduce a bug, or just generally making things worse. These commands are for you.

### Fixing the previous commit
Adding the `--amend` flag to a commit will change the most recent commit.
```bash
git commit --amend -m "This is the correct commit message"
```

Doing this with _no staged changes_ will simply rewrite the commit message. If you want to change the contents of the most recent commit as well, you can stage any changes and run the command as well.

```bash
git add file_to_update.txt
git commit --amend -m "This is the correct commit, and a file has been updated"
```

__Warning:__ Do not amend commits that have been published to your remote. This can cause problems for any people who have begun work based off of the most recently published commit.

### Undoing local changes

We're used to using the `checkout` command to move the `HEAD` to a different branch.
```bash
git checkout some-other-branch
```
This command moves our HEAD pointer (the version of our code / files our present working directory contains) to the latest commit on our newly viewed branch. However, this is not the only use for checkout. We can also use it to undo changes in our working directory.
```bash
git checkout HEAD file_to_undo.txt
```
This will revert `file_to_undo.txt` (working area included) to its last committed version. This allows you to selectively discard uncommitted changes to specific files.

If we want to undo all changes in the working directory to the most recent commit, we can use `git reset`

```bash
git reset HEAD
```
This will revert the HEAD back to the previous commit, while keeping our current changes in the working directory (not staged for commit)

```bash
git reset --soft HEAD
```
Using the `--soft` flag will reset the HEAD to the previous commit, leaving any changes in the staging area (staged and ready for commit)

```bash
git reset --hard HEAD
```
Using the `--hard` flag will reset everything to the previous commit, removing any changes from the working area. This hard reset is __unreversable__.

### Rolling back committed changes

All of the above commands work with undoing committed changes as well, only as opposed to passing them HEAD as an argument, you pass the commit hash you want to turn back to. Let's take a look at some options for reverting to old commits.

#### `revert` vs `reset`

```bash
$ git revert commit_hash
```

```bash
$ git reset commit_hash
# The --hard flag will delete all rolled back changes
# The --keep flag will keep them in your working area
```

`reset` and `revert` do very similar operations, with one important difference:
* `revert` will make a new commit where the code is turned back to where it was at `commit_hash`
* `reset` will erase any commits between the previous HEAD and `commit_hash`
  * If you use the `--keep` flag, the rolled back changes will appear as local changes in your working directory
  * Just like when undoing local changes, the `--soft` flag will keep the rolled back changes in the staging area

Becuase `reset` removes all previous commits and access to rolled back revisions of our files, `revert` is typically a safer bet for rolling back multiple commmits.

#### Check it out!

```bash
$ git checkout commit_hash

Note: checking out to commit_hash
You are in a 'detached HEAD' state...
```

While we have successfully moved our HEAD to look at an old commit, we've put ourselves in a bit of an odd spot. A detached HEAD state refers to the HEAD not pointing to an existing branch. This means that any commits made in this state will not belong to a branch, and will thus be easily loseable (especially if we switch to a new branch).

We can reaccess these commits by calling on their specific hashes, but that's pretty hard to remember. A better solution is to checkout to a new branch when we check out an old commit, then we can safely experiment in a new feature or test branch.

```bash
$ git checkout -b test-branch commit_hash
```

## Comparing commits / changes

So maybe you didn't fuck up. Or maybe you just need to compare old versions of a file. Or maybe you just want to remember what will get added to your next commit. Luckily git provides us with a nice and easy way to make these comparisons.

```bash
$ git log
```
[git log](https://git-scm.com/docs/git-log) will output the commit history in our local repository. We can use this to view commit messages, as well as see data about our revisions.
* Using the `--oneline` flag will give us an abbreviated output (commit messages, shortened hashes, remote and local HEAD locations)
* Using the `--patch` or `-p` flag will generate a [patch / diff](https://git-scm.com/docs/diff-generate-patch), or information about the modified parts of each file (aka _chunks_), in our log as well. This give us an easy way to see the differences between all our displayed revisions.

We can also generate a patch between two specific commits with [git diff](https://git-scm.com/docs/git-diff).
```bash
$ git diff
```
 Without any arguments, `git diff` will produce a patch between your local changes and the most recent commit

```bash
$ git diff commit_hash_one commit_hash_two
```
With two commit hashes as arguments, `git diff` will generate a patch comparing two revisions

```bash
$ git diff branch_one branch_two
```
With two branches as arguments, we can generate a patch comparing the most recent commits on each branch

### Reading Patches
Even though we know how to generate patches, reading them can be a bit overwhelming. Let's take a deeper look at what these big outputs mean.

```bash
diff --git a/example.md b/example.md # COMPARED FILES
index dbfaf94..1e591bc 100644 # FILE METADATA
--- a/example.md # MARKER FOR FILE b (OLD VERSION)
+++ b/example.md #MARKER FOR FILE b (NEW VERSION)
@@ -12,4 +12,6 @@ Hydrogen atoms billions upon billions hearts of the stars circumnavigated concept #CHUNK HEADER

# THIS IS A CHUNK

 Samuel L Ipsum:
 Do you see any Teletubbies in here? Do you see a slender plastic tag clipped to my shirt with my name printed on it? Do you see a little Asian child with a blank expression on his face sitting outside on a mechanical helicopter that shakes when you put quarters in it? No? Well, that's what you see at a toy store. And you must think you're in a toy store, because you're here shopping for an infant named Jeb.

-All these came from [Meet the Ipsums](https://meettheipsums.com/).
\ No newline at end of file
+All these came from [Meet the Ipsums](https://meettheipsums.com/).
+
+I'm adding new information so we can examine a diff!
\ No newline at end of file

# END OF CHUNK
```
* The chunk header tells us information about how many lines were modified in each file, after the initial line
* Anything with a `-` was changed in file a (the old version). Anything with a `+` was added in file b

## Dig Deeper
This lesson was largely pulled from the following two sites:
* [git docs](https://git-scm.com/)
* [git tower tutorial (use the command line instructions)](https://www.git-tower.com/learn/git/ebook/en/command-line/advanced-topics/undoing-things#start)
