# Git Notes

## What is Git?

Git is an open-source distributed version control system (DVCS) used primarily to track changes in source code during software development. Created by Linus Torvalds in 2005, it allows multiple developers to work on the same project simultaneously without overwriting each other’s work. 


## Core Concepts

* **Repository (Repo)**: A digital "folder" that stores your project’s files and the entire history of every change made to them.

* **Snapshots** (Commits): Unlike systems that track file differences, Git takes a "snapshot" of your entire project at a specific moment when you save your work (commit).

* **Distributed Nature**: Every developer has a full copy of the project history on their local machine, allowing them to work offline and providing security against server failure. 

## Standard Workflow

Git uses three main states for managing files: 
1. **Working Directory**: Where you currently edit your files.

1. **Staging Area (Index)**: A "rough draft" space where you pick which changes to include in your next save.

1. **Local Repository**: Where Git permanently stores the snapshots as commits. 

## Essential Commands

* **git init**: Create a new, empty Git repository.
* **git clone**: Copy an existing repository from a remote server to your computer.
* **git add**: Move changed files into the staging area.
* **git commit**: Permanently save staged changes to your history with a descriptive message.
* **git push**: Send your local commits to a remote repository (like GitHub).
* **git pull**: Download and merge the latest changes from a remote repository into your local work.

## Git vs. GitHub

While often used together, they are different:
Git is the actual software tool you install locally to manage versions.
GitHub (and competitors like GitLab or Bitbucket) is a web-based hosting service that stores your Git repositories in the cloud and provides collaboration tools like pull requests and issue tracking. 
Would you like a guide on how to install Git or a list of the most common troubleshooting commands for beginners?

