# Git!

## Quick definitions

Git is a Distributed Version Control System (yup non distributed exist). Being "distributed" allows you to own nearly all the files. 

GitHub started has an hosting plateform for remote repository of Git. Now it is a social media, a CI/CD plateform and more. Github is an "opinionated" version of Git. 

Few big difference between git and other VCS worth noting: git track change at the line level, just add stuff. 

## Git From the Command Line: Core commands

---------------------

`git clone <link>`: Clone a GitHub repository. The link to clone a repo is available in the green `Code` dropdown on the main page. Use the `SSH` option.

![clone_link](https://user-images.githubusercontent.com/33400922/137961118-9457bc17-5a54-44ce-a90d-92bdbc2f0704.PNG)

---------------------

`git pull`: Pull changes and files from GitHub into your local repository. By default (ie with a non modified steup) a `git pull` does a `git fetch` and a `git merge` (or attempt to).

---------------------

`git checkout -b <new_branch>`: Allow you to create a new branch and go into it (`git checkout` allow you to navigate git graph)  

---------------------

`git add (filename>`: Add new files to git

---------------------

`git commit`: The expanded form is `git commit -am 'commit message'`. This tells Git to commit everything on the working tree (`-a`) and add a message (`-m`).

---------------------

`git push`: Push local changes and files up to GitHub

## GitHub Workflow:

It is good practices to add important changes in a new branch and send it for a PR (Pull Request): 

- Create a PR with your commits (push it to remote repo) 
- Get it reviewed by someone on the team - in GH / their local repository.
   
You can inside of GitHub ask for a Reviewers, assign someone (Assignees) to do the merge (a Pull Request, is a merge inside of GitHub) or set up deadline (see at the right of the screan)

Once approved the PR you should not keep track of it inside of GitHub (the branch will still be inside of your local repo) and you should delete it. Github allow you to automatically do it: it is in the `setting` of the repo nearly at the bottom:

![Screenshot 2023-02-07 at 11 52 21 AM](https://user-images.githubusercontent.com/31417689/217310697-1a7134e3-c6e9-4f80-b5b3-fc30b684363d.png)


## Ressources 

- happy git with R : https://happygitwithr.com/
- git pro : https://git-scm.com/book/en/v2
- https://ohshitgit.com/ (NB most of it concerns git not github)
- A comics version: https://wizardzines.com/zines/oh-shit-git/ (a new zines should be available soon) 
