# Version control

We will use the Git distributed version control system for code release and submission. The baseline code for this assignment (and all assignments) are in the organization https://github.com/eec289q-f23/. In that organization you should be able to see all tutorials, homework instructions, project instructions, and your personal homework and project code repositories for each assignment. 

## Git Setup

After setting up your virtual machine, you will have to introduce yourself to Git (give Git your name and email so that you will be identified when you commit changes).

Where you are developing code, either locally and HPC2 or just HPC2, run the following. Use the GitHub username and email that you accepted the invitation to the organization with. 

```console
$ git config --global user.name <GITHUB_USERNAME>
$ git config --global user.email <UCDAVIS_EMAIL>
```

## Git Basics

See [Setting Up a Repository](https://www.atlassian.com/git/tutorials/setting-up-a-repository) for a great introduction to Git. Namely we will care most about `git clone`, `git add`, `git commit`, `git push` and `git status`.

### Example Workflow

- Clone your repository to your development machine. For example, if you wanted to clone the `tutorials` repository, you'd go to https://github.com/eec289q-f23/tutorials then click the green `<> Code>` button and click on SSH, copy this string and run the following to clone to a `tutorials` folder locally. 
  - This was an example, in reality you will clone your homeworks and projects in the same way

```console
$ git clone git@github.com:eec289q-f23/tutorials.git
```

- You can open up this project folder in VSCode or another IDE, and start developing. When you have made some notable progress, e.g. you finish one part of an assignment, commit your work with `git add`, `git status`, `git commit` and `git push`
  - If you add files you didn't want to add, use `git restore --staged <file>` to unstage the file, without deleting it locally
  - Run `git status` liberally to see what has changed (e.g. after each command below)
  - Update your `.gitignore` file to tell Git about any file types it should ignore, e.g. C/C++ build files and executables

```console
# See the files that have changed
$ git status

# Add them all with -A, or individually by filename/path
$ git add -A  

# Commit the files with a message
$ git commit -m "Your commit message"

# Push the commit to your repo
$ git push

# Continue development..
```

For a deeper dive, see the other Atlassian tutorials, or search for more Git tutorials online.
  - [Beginner](https://www.atlassian.com/git/tutorials/what-is-version-control)
    - Git Basics
  - [Learn Git](https://www.atlassian.com/git/tutorials/learn-git-with-bitbucket-cloud)
    - git clone, git config, git add, git status, git commit, git push, git pull, git branch, git checkout, and git merge
  - [Collaborating](https://www.atlassian.com/git/tutorials/syncing)
    - git remote, git fetch, git push, git pull, git branch, git checkout, git merge
  - [Advanced Git Tutorials](https://www.atlassian.com/git/tutorials/advanced-overview)
    - Everything... 