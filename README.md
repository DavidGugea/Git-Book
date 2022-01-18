# Git book

## 1. Git in 10 minutes

## 2. Learning by doing

## 3. Git basics

## 4. Analysing data in a git repository

## 5. GitHub

## 6. GitLab

## 7. Azure DevOps, Bitbucket, Gitea and Gitolite

## 8. Workflows

## 9. Working techniques

## 10. Git in practice

## 11. Git problems and their solutions

## 12. Command reference

---

# 3. Git basics

## Internals

The **"git database"** is the ```.git``` folder that is generated when you write ```git init```. 

![Git database overview](ScreenshotsForNotes/Chapter3/gitDatabaseOverview.PNG)

* **HEAD** is a text file that points to the current commit inside the current branch.
* The **branches** folder contains abbreviations for ```git fetch```, ```git pull``` and ```git push```. It's considered obsolete and will be deleted in the future.
* The **config** file is a configuration file for the repository.
* The **description** file represents a short description of the repository in text form.
* **hooks** is a folder containing scripts that have to run automatically in certain situations.
* **info/exclude** is a supplmenet similiar to a ```.gitignore```-file. It can be used in order to add files that will be ignored by git, just like in a ```.gitignore``` but without having to change the ```.gitignore``` file for all the other developers that are working in your project.
* The **index** ( not shown in the screenshot ) represents the staging area. This *file* is created whenever you use ```git add```. The binary file contains references to modified files, that were added to the staging area using ```git add```. ( stage = index = cache )
* In **logs** you can find all the reflogs.
* In the **objects** folder you will find hash codes for all the objects that were saved. These objects can be blobs, trees of changes, commits or tags.
* In the refs folder you can find references to commits/objects. The references points to the latest commits ( Heads ).

## Git objects

Git objects can be found in the ```./git/objects``` folder and can be of one of the following 4 types:

1. Commit: A commit object contains references to the tree of changes, the parent commit, the committer and the commit message, etc.. This data is called meta-data.
2. Blob: A blob ( binary large object ) contains a saved file. They are compressed binary objects that save a lot of space.
3. Tree: A tree object contains a list of file names with hashcodes, that point to certain blobs.
4. Tags: There are more types of tags. The easiest forms of tags are ```lightweight tags``` and these are text-files that are saved in ```/.git/objects/tags```.

## Git references

Git references ( refs ) are small files that reference a certain object ( a commit object ). For example you can find the the hashcode of the last commit object of the ```master``` branch inside ```refs/heads/master```.

You can use ```git show-ref``` to see all the references.

## Commits

A commit is a snapshot of the current state of the branch that you are currently on.

If you have a new file or you have deleted a commited file you will have to add the stages into the staging/cache/index area. All the changes that you make are in your current "workspace", that being the current branch that you are working on. In order to save those changes into the staging area you need to use ```git add/git stage```. In order to save these changes in to your repository, you have to **commit** them using ```git commit```. Here is a summary:

![Workspace -> Stage -> Repo ](ScreenshotsForNotes/Chapter3/WorkspaceStageRepo.PNG)

In the following, I will be talking about what happens internally with your files.

Let's say that you create a new repository and in this repository you add a file *foo.txt*. When you write ```git add foo.txt```, a new blob will be created for this file inside ```./git/objects```. This hash will look something like this: ```4619e73b2bd3f2f8bc7de3b5848cce2633d5f8d```. Inisde the objects folder, you will see a folder with the first two chars of the hashcode ( the sha code ), in this case *46*, and then the rest, so you will have ```./git/objects/46/19e73b2bd3f2f8bc7de3b5848cce2633d5f8d```.

In order to look at this blob, you can write ```git cat-file -p 4619``` ( you only need the first 4 chars ). If you want to see of what type it is, you can write ```git cat-file -t 4619```. *-p* stays for pretty print while *-t* stays for type.

If you create a commit for this file, you will get 2 more hashcodes. One hashcode will be for the tree object and the other hash code will be for the commit object.

* Commit objects contain meta data about the commit ( who ? when ?, etc ). It contains references to the last commit ( the parent commit ) of that branch and to the tree of changes.
* The tree object contains references to all the blobs in the active branch.
* For every staged/commited file there is a blob.

Git commands:

| Command | Description |
| ----------- | ----------- |
| ```git add``` | Adds a file to the staging area |
| ```git commit``` | Commits a file from the staging area to the repository |
| ```git cat-file -p <hash>```| Returns data about the given object ( blob, tree or commit ) |
| ```git cat-file -t <hash>```| Returns the type of object ( blob, tree or commit ) |
| ```git mv <oldfilename> <newfilename>```| Changes the name of the file |
| ```git mv <file> <into-another-directory>```| Changes the directory of a file |
| ```git rm```| Removes a file |

## Commit-Undo

### Reseting from staging area -> ```git reset```

If you have added a file to your staging area using ```git add/stage``` and you don't want that file to be in the staging area anymore so it won't be taken into consideration by the next commit you can use ```git reset <file>```. If you want to take all the files from the staging area use ```git reset``` ( with no parameters ).

### Restoring changes to last commit -> ```git restore```

If you have made changes to a file since the last commit and you don't want these changes anymore, you want to go back to the last commit, you can use ```git restore <file>``` and if you have made changes to multiple files and you want to undo these changes to all files that you've modified and go back to the last commit you can use ```git restore .```.

### Looking at files from older version -> ```git show```

If you want to see what a file looked like at a specific commit you can use ```git show <commit-hash/HEAD~n>:<file>```.
If you write ```git show HEAD:foo.txt```, it will show you how the file ```foo.txt``` looked like the last commit.
If you write ```git show HEAD~1:foo.txt```, it will show you how the file ```foo.txt``` looked like 2 commits ago.

Remember that you can see the **HEAD** number, the commit hash and commit message using ```git reflog```.

You can also look back at files from a specific commit. Example:

```git show ab3f:foo.txt```

This will show you how ```foo.txt``` looked like at that specific commit hash.

### Looking at changes from different version / Comparing different versions -> ```git diff```

You can use ```git diff``` to see the exact changes that have been made to a file.

```git diff <commmit-hash/HEAD~n> <file>```

### Restoring files from older versions -> ```git restore```

If you want to resoter files from older versions you can use ```git restore -s <commit-hash/HEAD~n> <file>```
The parameter ```-s``` stands for ```--source``` and it requires a tree of changes.

### Revoking commits -> ```git revert```

You can go back to older commits using ```git revert <commit-hash/HEAD~n>```. 
You can revoke more commits at once, but you will have to write a commit message for each of them which is a waste of time, so you can use the ```git revert -n``` which stands for ```git revert --no-commit```:

```git revert -n HEAD~2^..HEAD```

or 

```git revert -n abcd^..qwer```

It's important for the structure to look like this:

```git revert -n <commit-hash/HEAD~n>^..<commit-hash/HEAD~n>```

### Revoking commits -> ```git reset```

You can also revoke commits using ```git reset --hard/--soft <commit-hash/HEAD~n>```, but the difference is that the ```HEAD``` will point out to a different commit, you won't have to create a new commit. You will also see another history of changes when writing ```git log``` ( you can always write ```git reflog``` to see the changes that you've made and go back to commits that can't be seen by ```git log```).

The difference between ```--hard``` and ```--soft``` is that the changes that you've made won't be saved with ```--hard```, they will be overwritten by the commit that you're revoking.

### Temporarily changing to an older commit -> ```git checkout```

You can use ```git checkout``` in order to temporarily go to an older commit, you HEAD will be a ```detached HEAD```:

```git checkout <commit-hash/HEAD~n>```.

## Branches

Branches are widely used in teams in order to split the work in different tasks. You might one to work on a new feature for an app, but not push the code on the master branch directly because your work might not be finished yet or not tested. 
You can solve that problem by using branches.

In order to create a branch, you can use ```git branch <branch-name>``` and if you want to see a list of all branches ```git branch --list```.

You can also use ```git checkout``` in order to leave the current branch that you are on and go on another branch for a short time: ```git checkout <branch-name>```.

A branch is nothing else than a ```commit object```. The branches that you make will appear in ```.git/refs/heads```. This is why branching happens so fast and easy in git.

## Merge

If you've been working on a new feature on a special branch and you're done with that feature, you will want to integrate it into the main project, into the ```master``` branch. In order to do that you have to go back to the master branch and ```merge``` it using ```git merge branch_name```.

If you want to delete the branch directly after merging you can use:

```git merge -d branch_name```

If you want to see a list of your branches use:

```git branch --list```

You can also write ```--merged```/```-no-merged``` if you want to see if the branches were merged into the master branch or if people are still working on them.
You can also merge the ```master``` branch into your feature branch. You don't always have to merge the feature branch into your master branch, you can also do the opposite. If you've been working for a long time on your feature, you might want to stay up to date with what's happening on the real project ( on the ```master``` branch ); in order to do that you have to merge the ```master``` branch into your feature branch.

There are 2 types of merges:

* Fast-Forward Merge
* Normal Merge

### Fast-Forward Merge

Imagine you are working on the branch ```feature``` and you have made 2 commits on it: C and D. On the ```master``` branch, nothing has changed ( there haven't been any new commits ) since you've changed to the ```feature``` branch, so you only have commits A and B.

This is how your branches look like:

**master: A B**
**feature: A B C D**

If you want to merge the feature branch into the master branch, that is called a **Fast-Forward merge**.
A **Fast-Forward merge** is not a real merge since the only thing that is really happening is changing the *HEAD* pointer to the last commit of the merged branch.

### Normal Merge

A normal merge is when your master branch has at least 1 commit that the branch that you are trying to merge doesn't have:

**master: A B E**
**feature: A B C D**

In this case, you will have to write a special *merge commit message*.

### Octopus Merge

The octopus merge is when you merge multiple branches in one merge:

```git merge branch1 branch2 branch3``` 

**This is a very bad practice** since it's very hard to debug merge conflicts in this way, even if there are only 2 branches that are being merged together. The only advantage of this type of merge is that you only have to write one single commit message; however **it is a better practice to merge every single branch on its own**.

### Merging Strategies

There are multiple merging strategies that you can pick using

```git merge -s <strategy>```

* resolve
* recursive ( default )
* octopus
* subtree

From the git manual:

> resolve - This can only resolve two heads (i.e. the current branch and another branch you pulled from) using 3-way merge algorithm. It tries to carefully detect criss-cross merge ambiguities and is considered generally safe and fast.

> recursive - This can only resolve two heads using 3-way merge algorithm. When there are more than one common ancestors that can be used for 3-way merge, it creates a merged tree of the common ancestors and uses that as the reference tree for the 3-way merge. This has been reported to result in fewer merge conflicts without causing mis-merges by tests done on actual merge commits taken from Linux 2.6 kernel development history. Additionally this can detect and handle merges involving renames. This is the default merge strategy when pulling or merging one branch.

> octopus - This resolves more than two-head case, but refuses to do complex merge that needs manual resolution. It is primarily meant to be used for bundling topic branch heads together. This is the default merge strategy when pulling or merging more than one branches.

> ours - This resolves any number of heads, but the result of the merge is always the current branch head. It is meant to be used to supersede old development history of side branches.

> subtree - This is a modified recursive strategy. When merging trees A and B, if B corresponds to a subtree of A, B is first adjusted to match the tree structure of A, instead of reading the trees at the same level. This adjustment is also done to the common ancestor tree.

A short summary of them:

*Resolve* and *recursive* are almost the same but recursive results in fewer merge conflicts. We've already talked about the octopus merging strategy. If you are using ```git merge branch1 branch2 branch3``` you don't have to specify the *octopus* strategy since it's used automatically. The *ours* strategy is used when you want to pull in another head but throw away the changes that the head introduced. The *subtree* is used when you want to merge a project into a subdirectory of your current git project.

### Cherry Picking

You're working on ```branch1``` and already have a few commits ahead of ```master```. You go back to ```master``` in order to fix some bugs and make a few other commits on ```master``` as well and then go back to ```branch1```. You realize later that the bug that you've fixed on the ```master``` branch needs to be fixed on the ```branch1``` as well, so you could tehnically merge the ```master``` branch into your feature branch; however you realize that other commits have been made to the ```master``` branch that you don't want into your feature branch, meaning that you can't merge that branch. **In order to fix this problem you must cherry pick the commit where you've fixed the bug.

```git cherry-pick <commit-hash>```

Cherry picking means 'merging' one single commit. You are not merging the entire branch, you are just taking the changes that you've made on a specific commit.
The following image illustrates cherry picking:

![Cherry Pick Example](ScreenshotsForNotes/Chapter3/CherryPickExample.PNG)

## Stash

Stashing is used when you want to save the changes that you've made, just like a commit, but not commit them.
If you are working for example on a feature and you have to switch to another branch in order to fix a bug, you might not want to commit the changes that you've made since they are probably not done yet and the feature isn't working properly yet.
In order to save the changes without making a commit you can use

```git stash```

After solving the bug on the other branch that you had to go to, if you want your changes back use

```git stash pop```

The stash works like a ```stack``` ( that's why you use *git stash pop* ). You can stash multiple changes. The first changes that you've stashed will be the last ones to get popped out of the stash

Using ```git stash list``` you get to see the stack of stashes that you have.
You can use ```git stash -p stash@{<n>}``` in order to get information about a certain stash.
Use ```git stash drop``` in order to delete one stash and ```git stash clear``` in order to delete all the stashes that you've made.

You can find references to all the stashes in *.git/refs/stash*.