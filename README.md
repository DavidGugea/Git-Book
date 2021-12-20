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
| ```git cat-file -p <hash>```| Returns data about the given object ( blob, tree or commit ) |
| ```git cat-file -t <hash>```| Returns the type of object ( blob, tree or commit ) |
| ```git mv <oldfilename> <newfilename>```| Changes the name of the file |
| ```git mv <file> <into-another-directory>```| Changes the directory of a file |
| ```git rm```| Removes a file |

