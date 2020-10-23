# Git, daily

## Git workflow on a daily basis

Using a **versioning system** instead of storing locally your changes or backups (like i.e: inside .zip files with silly names like *yesterday-works*, *feature-date* or *latest*, *superlatest*, *latest-really*), helps to keep track with meaningful comments on every change you made in your files, and helps to revert a project to an earlier state and collaborate with other people on the same project.

Git is a complex and powerful tool, the learning curve can be steep or frightening at times.  
I'll just showcase some common ways I use **Git on a daily basis** to get you started.

# Getting started

- Download Git (and Git Bash) https://git-scm.com/downloads

# Online Services

You don't necessarily need to save your changes to a remote online service. You can use Git locally - only on your machine. But **what if** you lose your laptop, or you mistakenly erase your project? What if you want to share your project with the world or allow other people to cooperate on the project?  

In that case you really want to use one of the popular Git cloud services like i.e: GitHub, GitLab, and others. The only difference is that in that case you `push` your changes to that remote service.  

## Account and SSH

**Create an account** on a desired service and follow the **instructions** to setup your SSH key.  
An SSH key is used to grant your machine access credentials to the remote service repository.  

- **GitLab**: [SSH](https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair) and [Keys](https://gitlab.com/profile/keys)  
- **GitHub**: [SSH](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/connecting-to-github-with-ssh) and [Keys](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account) 
- or any other of your choice 

# Git Config and command aliases

First you need to set the **user** (you), inside the Git configuration file `.gitconfig`.  
Open your terminal and enter the following lines, replacing the below values with your own data, and make sure to use the **same email** you used while registering to the preferred online service: 

```sh
$ git config --global user.name "My Name" # Your name
$ git config --global user.email "my.name@example.com" # The email you used on GitHub, GitLab, etc
```

<sub>*PS: The above prefix `$` symbols a terminal **Shell command**, you don't actually need to type that character in!*</sub>

Git commands can be tedious to type, what if instead of typing `git push --force-with-lease` we could just do: `git ph`. Here's my suggestion for the additional configuration parameters, feel free to modify or expand it.  

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

# Creating / getting a project

## Create a new Git Project

If either you already have a project folder on your machine or you're starting from scratch, 
go to your online Git service, create a **New Project** and follow the suggested steps.  

## Get a Project - Clone

To **clone** an existing repository head to the project page and under ***"Clone"*** copy the repository URL i.e: `git@gitlab.com:some-group/some-repo.git`  

`cd` into a folder you want to clone that project into i.e: `projects` and execute in shell:

```sh
$ git clone git@gitlab.com:some-group/some-repo.git
```

you should now see your cloned repository:

<pre>
projects/
    some-repo/
        .git
        # and other project files and folders
</pre>


# Understanding

## Commits and Branches  

By default a Git project has a *main* or (often referred as) ***master*** branch.  
When creating a project one usually creates some project files and **commits** them with a name i.e: "First commit".  

Commits are stored with a reference, a 40 characters length SHA-1 hash like i.e: `7ebd3765807343fe4e8ac3ec889313446e5aa158`, but for simplicity sake I'll use here a single character like `a, b, c, etc...`  


```
(master)    A
```



Say now, after editing some files and fixing some code, one additional commit is added (`"Create form"`):

```
(master)    a───B
```

# Workflow

## New Branch

You usually create a new branch whenever you want to add a new feature or experiment to your project, but you don't want to interfere with other developers working on the branch you diverged from (i.e: `master`):

```sh
# (master)
$ git ch -b feature-ajax
# (feature-ajax)
```

At this stage you can think of your new branch as an exact *copy* of the branch you diverged from on which you can start experimenting.

```
(master)    a───b
(feature-ajax)  ╰─
```
```sh
# Work on your code. Add new files and create a commit with message:
# (feature-ajax)
$ git st # to see what files we changed
$ git cm "WIP Create JS AJAX forms" # Alias for git add -A && git commit -m 
```
```
(master)    a───b
(feature-ajax)  ╰─X
```

## Interactive Rebase

After adding another commit i.e: `"WIP Create error callbacks"`

```
(master)    a───b
(feature-ajax)  ╰─x───Y
```

the two *"WIP"* (Work In Progress) are not a nice thing to share with other developers.  
Now you can **squash** the two commits into one single commit with a more appropriate message.   

```sh
# (feature-ajax)
$ git rebase -i HEAD~2 # take two latest commits for rebase
```

<sub>PS: instead of `HEAD~2` you could have used the hash of a desired commit.   
Use `git log` to see a list of commits with messages and its hash.</sub>

You will be prompted with instructions and the list of your commits, something like:

```
pick "WIP Create JS AJAX forms"
pick "WIP Create error callbacks"
```

which will be resolved from bottom-top. Set a `s` (**squash**) flag to the second commit to join it with the first one. Save and close the file.

```
pick "WIP Create JS AJAX forms"
s "WIP Create error callbacks"
```

At this point you'll be prompted to create a new commit name i.e: `"Create JS AJAX forms"`.  
Save and close the interactive editor.  
The two commits are now joined into one with a new hash value:

```
(master)    a───b
(feature-ajax)  ╰─K
```

## Push new branch to remote

After you're happy with your work for the day, you should push your work to the remote service.   
Since the branch we created is only on our machine we (this time only) want to 

```sh
# (feature-ajax)
git ph -u origin feature-ajax # Send branch and commits to remote
```

<sub>PS: `-u` flag is a shorthand for `--set-upstream`</sub>

## Pull/Push  with Rebase

Since we know now that our branch exists on the remote and other developers could have also contributed to the code on that branch, we **always** want to first bring the remote work to our machine, apply our changes on-top and only than - `push` to remote:

```sh
# (feature-ajax)
git st # See changes we made to our files
git cm "Add form label icons" # adds files and commits with a meaningful message
git pl # Brings other people work and put our work on-top
git ph # Push everything back to remote
```

PS: Peview the **Aliases** table if you forgot what `st, cm, pl, ph` are.

Our tree with our latest commit `l` put on top of other people commits (`x`, `y`) might now look like:

```
(master)    a───b
(feature-ajax)  ╰─k───x───y───L 
```

## Merge 

Time to merge a feature back to where it derived from.  
We are happy with how our feature ended up, well tested, working and shiny, it's time to put it into `master` so it can be i.e: shipped to production: 

```sh
# (feature-ajax)
$ git ch master # checkout to master branch
# (master)
$ git pl # bring eventual changes on master from remote
$ git ch - # go back (or use: git ch feature-ajax)  
# (feature-ajax)
$ git rebase master # Rebase with master and fix possible conflicts  
$ git ch - # go back to master (or use: git ch master)
# (master)
$ git merge feature-ajax # bring our feature branch on-top of master
$ git ph # push master back to remote
```










