# Git for Gits

Intended to be somewhere between a cheatsheet and a full guide, this document explains git functionality.

I recommend using for quicker reference [GitLab's cheatsheet](https://about.gitlab.com/images/press/git-cheat-sheet.pdf).


## Overview

Git is a version control software, it's fairly useful for both backing up and recovering work as well as working collaboratively with a team.

You can install git from the [git website](https://git-scm.com/).

Whilst git is decentralised in essence, most people use repository hosting services such as [GitHub](https://github.com/), [BitBucket](https://bitbucket.org/) or [GitLab](https://gitlab.com/). They are all relatively similar whilst offering slightly different features in addition to the git functionality.

Git has various GUI tools that can be used but to understand it it is better to learn the command line for it to understand what those GUIs are doing and in case you have to use a repo on a remote machine without such an interface.

Learning git is a process but in time will become second nature.

## Git Basics

Essential git commands and tips.

## Git Repositories

A repository is a folder containing files and a .git folder. 

## .git Folder

 When a git repository is initialised a hidden folder '.git' is created containing all the git related information such as commit history, branches etc.  Git commands will only work in the directory that the .git folder is located in - if you navigate to another folder, git cannot tell which repository you are referring to. If subdirectories of a repo also contain a .git folder, git will run into issues. Either delete the .git folder (which will remove all commit history) or use the method described below to add one repository to another.

## Cloning a Remote Repo

To create a copy of a repository hosted on a remote server (ie. GitHub) you need to use the clone command. Move to the folder you want the repository folder in and enter:

`git clone [repo url]`

To clone this repository to your machine:

`git clone https://github.com/laportag/learning_material.git`



<!-- -------------------------------------------------------- -->

## Initialising a Repository

To initialise a folder as a git repository move to the folder in your terminal and run:

`git init` 

You can also run:

`git init [repo name]`

to create a new folder of [repo name] and initialise the repository.

Repositories can also be initialised on such GitHub and other hosting sites by pressing 'New Repository'.

<!-- -------------------------------------------------------- -->


## Remotes

A 'remote' in git is essentially the location the repository is stored at on a local machine. A repository can be stored in multiple remote locations and thus can have multiple remotes. If you clone from a remote repository, git will automatically configure the remote for you. If you initialise the repository locally and wish to push to GitHub (for instance), you will need to set a remote so git knows where to send the files. The first remote added to a git repository is often called *origin*, this is **not** a keyword and a remote can be named anything that is not a keyword. To add a remote to your repository:

`git remote add [remote name] [remote url]`

I made this repo on GitHub and cloned it down so the origin remote was already set. Had I initialised the git repository locally I would have set the remote like so:

`git remote add origin https://github.com/laportag/learning_material.git`

To reiterate, the name origin isn't necessary, but it is the default name given to the first 

To view the remotes of the repository:

`git remote -v`

The *-v* tag stands for verbose and generally provides more information in the output. In the case of git, the *-v* tag also displays the url of each remote in addition to the name it has been given.

To change the url of a remote:

`git remote set-url [remote name] [remote url]`

To rename a remote:

`git remote rename [old name] [new name]`

<!-- -------------------------------------------------------- -->

## Using Git to Backup Your Files - The Git Workflow 

The proccess of adding files to a staging area, committing the staged changes and pushing the commit to a remote are is known as the git workflow. Files that have not been added to the staging area will not be committed and not pushed to the remote. Only files that differ from the files on the remote can be added.

Don't worry about why you have to do all these steps, just accept that an upload consists of these three steps. 

Each significant change to your work should be followed by you using:  
`git add [file]`  
`git commit -m [message]`  
`git push [remote name] [branch name]`

### Updating a Local Repository - git pull

To update your local version of the repository with changes that have been uploaded to a remote you will need to use git pull:

`git pull [remote name]`

### Adding Changes to the Staging Area - git add

To add a file to the 'staging area' you use:

`git add [file name]`

To add all the files in the directory:

`git add *`

\* is a 'wildcard' that means all, if you use *.txt it will add all the text files. If you just want all the files you can also use a full stop:

`git add .`

The full

### git commit

To commit your staged changes ready to be pushed:

`git commit -m "[your commit message]"`

If there is no whitespace in your message you do not need the quotes. The *-m* tag stands for message. Git will not let you commit without a message and if you forget to include one in the command it will send you into a CLI text editor (nano/vim) where you can write one and subsequently exit the editor.

### git push

To push your committed changes to a remote:

`git push [remote name] [remote branch]

The default branch will likely either be main or master. GitHub changed their default branch from master to move away from unpleasant terminology but older repos will often have a master branch instead of a main. The name itself doesn't matter, but you will have to make sure you are using the correct one.

If you are anything like me you will run *git push* and forget to add the remote and branch arguments. Lucky for us, git throws you a warning telling you to use them and also how to set default options so you can be a lazy git and just use *git push*, see below - Setting the Upstream

### Example

Below demonstrates how I uploaded this markdown file and all the subsequent changes to the GitHub remote.

In this scenario the git repo is already initialised and the remote named *origin* has been added and points towards https://github.com/laportag/learning_material.git. Changes have been made to the git_for_gits.md file since the last commit.

`git add git_for_gits.md`  
`git commit -m "workflow example written"`  
`git push origin main`  

### Setting the Upstream

Sounds more complicated than it needs to (there's a trend forming). You can set both a default remote for your local repository so you do not have to specify it when pushing. You can set this default (known as the upstream) using:

### Additional Useful Workflow Commands (diff and status)

## Branches

### Feature Branch Model of Software Development

## Log

## Removing Files from a Repoository

## Recovering Previous Versions of a Repository



<!-- -------------------------------------------------------- -->

## More Advanced Git

## .gitignore

### Merging

### Amalgamate Two Repositories with Commit History

### Stash

