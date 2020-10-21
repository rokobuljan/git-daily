# Git, daily

## Git workflow on a daily basis

Using a **versioning system** instead of storing locally your changes or backups (like i.e: inside .zip files with silly names like *yesterday-works*, *feature-date* or *latest*, *superlatest*, *latest-really*), helps to keep track with meaningful comments on every change you made in your files, and helps to revert a project to an earlier state and collaborate with other people on the same project.

Git is a complex and powerful tool, the learning curve can be steep or frightening at times.  
I'll just showcase some common ways I use **Git on a daily basis** to get you started.

# Getting started

- Download Git (and Git Bash) https://git-scm.com/downloads

# Online Services

You don't necessarily need to save your changes to a remote online service. You can use Git locally - only on your machine. But what if you lose your laptop, or you mistakenly erase your project? What if you want to share your project with the world or allow other people to cooperate on the project?  

In that case you really want to use one of the popular Git cloud services like i.e: GitHub, GitLab, and others. The only difference is that in that case you `push` your changes to that remote service.  

## Account and SSH

**Create an account** on a desired service and follow the **instructions** to setup your SSH key.  
An SSH key is used to grant your machine access credentials to the remote service repository.  

- **GitLab**: [SSH](https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair) and [Keys](https://gitlab.com/profile/keys)  
- **GitHub**: [SSH](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/connecting-to-github-with-ssh) and [Keys](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account) 
- or any other of your choice 

# Git Config and command aliases

First you need to store on our machine the info about you, the **author, committer** - inside the Git configuration file `.gitconfig`.  
Open your terminal and enter the following lines, replacing the below values with your own data, and make sure to use the **same email** you used while registering in the preferred online service: 

```sh
$ git config --global user.name "My Name" # Your name
$ git config --global user.email "my.name@example.com" # The email you used on GitHub, GitLab, etc
```

Git commands can be tedoius to type, what if instead of typing `git push --force-with-lease` we could just do: `git ph`. Here's my suggestion for the additional configuration parameters, feel free to modify or expand it.  

Open in your favorite code editor `~/.gitconfig`  
You should already see `[user]` and the key/values we entered previously.   
Add the following:

```sh
[alias]
    ali = config --get-regexp alias # Print a list of this aliases
    pl = pull --rebase
    ph = push --force-with-lease
    ch = checkout
    co = commit # Simple commit
    cm = !git add -A && git commit -m # Commit with message (Suffix with "Some message") 
    st = status # Get current branch local status
    br = branch # List local branches
    lg = log --graph --pretty=format:"%C(yellow)%h%Creset%C(cyan)%d%Creset\\ %C(cyan)(%cr)%Creset\\ %C(green)%cn%Creset\\ %s" # Pretty print tree
    rank = shortlog -sn --no-merges # who works harder :)
    diffc = diff --cached HEAD^ # See changes since last commit
[pull]
    rebase = true
[push]
    followTags = true # push only annotated tags 
[diff]
    tool = default-difftool
[difftool "default-difftool"]
    cmd = code --wait --diff $LOCAL $REMOTE
[branch]
    autosetuprebase = always # Force all new branches to automatically use rebase
[core]
    editor = code --wait
    autocrlf = false
    eol = lf
```

**IMPORTANT: From now on we'll make use of the above *aliases***. Less typing, more fun.   
PS: If at some point you forgot some alias shorthand, just run in terminal `git ali` to see a list of all the available aliases.










