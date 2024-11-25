# Git!

## Quick definitions

Git is a Distributed Version Control System (yup non distributed exist). Being "distributed" allows you to own nearly all the files. 

GitHub started has an hosting platform for remote repository of Git. Now it is a social media, a forge, a CI/CD platform and more. Github is an "opinionated" version of Git. 

Few big difference between git and other VCS worth noting: git track change at the line level and it just add stuff. 

Git works with a "tree" structure hence you can keep that analogy in mind. You are always working on a "branch"  (by default just one branch: `main`/`master`, `trunk` is another common name).  

## Git From the Command Line: Core commands

---------------------

`git clone <link>`: Clone a GitHub repository. The link to clone a repo is available in the green `Code` dropdown on the main page. Use the `SSH` option.

![clone_link](https://user-images.githubusercontent.com/33400922/137961118-9457bc17-5a54-44ce-a90d-92bdbc2f0704.PNG)

---------------------

`git pull`: Pull changes and files from GitHub into your local repository. By default (ie with a non modified setup) a `git pull` does a `git fetch` and a `git merge` (or attempt to).

---------------------

`git checkout -b <new_branch>`: Allow you to create a new branch and go into it (`git checkout` allow you to navigate git graph)  

---------------------

`git add (filename>`: Add new files to git

---------------------

`git commit`: The expanded form is `git commit -am 'commit message'`. This tells Git to commit everything on the working tree (`-a`) and add a message (`-m`).

---------------------

`git status` is telling you what is going one. `git diff` is more precise and usually integrated in your IDE. 

---------------------

`git push`: Push local changes and files up to GitHub

## A typical git/GitHub set up / workflow

You will have a repository in one local computer(s) and one in GH. 
Sometimes you can have one remote repository in your GH space and one in CORI (this setup is more common when contributing to an open source repository). 
The benefit is that you can mess up your GH space while not screwing the CORI's one (or other org repo). 
The draw backs are that you need to be more careful how your git is setup (ie what is define as your `remotes` and `upstream`). 

You can also use git without having a remote repository. 

The connection to git is done with SSH. 

In git a file can be not tracked (see `.gitignore`/`.gitkeep`), unmodified, modified, staged. If a file is tracked (you asked git to keep track of it with `git add [file]`) then this file can be modified or not. The first time you add a file it is modified because git knows nothing about it. Then you can "staged" it, if a file "modified" is "staged" it goes to unmodified (until modification were done on it).  A commit is creating a new "node" in your tree of files that are "staged". 


## GitHub Workflow:

It is good practices to add important changes in a new branch and send it for a PR (Pull Request): 

- Create a PR with your commits (push it to remote repo) 
- Verify it does not conflict to the `main` branch, and eventually solve those conflicts    
- Get it reviewed by someone on the team - in GH / their local repository.    
- The "owner" of the repository should be the one to allow the merge to `main` (this is particular important in everything that is using git as a deployment tool).    
   
You can inside of GitHub ask for a Reviewers, assign someone (Assignees) to do the merge (a Pull Request, is a merge inside of GitHub). 

Once approved the PR you should not keep track of it inside of GitHub (the branch will still be inside of your local repo) and you should delete it. Github allow you to automatically do it: it is in the `setting` of the repo nearly at the bottom:

You can check your PR using this link [https://github.com/pulls/assigned](https://github.com/pulls/assigned) in the same way you can look at your issues: [https://github.com/issues](https://github.com/issues).

![Screenshot 2023-02-07 at 11 52 21 AM](https://user-images.githubusercontent.com/31417689/217310697-1a7134e3-c6e9-4f80-b5b3-fc30b684363d.png)


## Resources 

- Happy git with R : https://happygitwithr.com/
- Git pro : https://git-scm.com/book/en/v2
- https://ohshitgit.com/ (NB most of it concerns git not github)
- A comics version: https://wizardzines.com/zines/oh-shit-git/ (a new zines should be available soon)
- MIT missing semester: https://missing.csail.mit.edu/2020/version-control/
