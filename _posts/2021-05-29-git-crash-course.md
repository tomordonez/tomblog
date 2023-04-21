---
layout: post
title: "Git Crash Course"
description: "Just another git work flow"
author: tom
image: assets/images/1.jpg
tags: [git, github]
---

I have been studying Git and here is what I learned.

*Updated January 2022*

* Last year I used the git branching model as shown [here](https://nvie.com/posts/a-successful-git-branching-model/) to track versions of a dashboard I built using Google Maps API in GCP, Python, and PowerBI. However I found it hard to follow in a CD environment as described at the beginning of that post. It's a good process as it's mentioned, to build software that requires version maintenance. A good example is Kubernetes as seen in the changelog [here](https://github.com/kubernetes/kubernetes/tree/master/CHANGELOG)
* I moved on to using the recommended [Github flow](https://docs.github.com/en/get-started/quickstart/github-flow) for continuous delivery with [JIRA integration](https://support.atlassian.com/jira-cloud-administration/docs/integrate-with-development-tools/).

I also missed the bus when they decided to replace the naming convention from `master` to `main` as seen on the github blog [here](https://github.com/github/renaming).

## Github flow and JIRA

* Integrate with JIRA
* Protect main branch
* From main, create a branch with the JIRA issue ID.
* Make changes on the branch
* Create a PR
* Merge PR
* Delete branch

**Summary**

Step 1: Pull main changes, create and work on a branch (lookup JIRA's issue ID), and push the branch.

    $ git checkout main
    (main)$ git pull
    $ git checkout -b ABC-123/add-login
    (ABC-123/add-login)$
    (ABC-123/add-login)$ echo "Something" > awesome.txt
    (ABC-123/add-login)$ git commit -m "ABC-123 added awesome"
    (ABC-123/add-login)$ git push -u origin ABC-123/add-login

Step 2: Go to Github, create a PR, merge, delete branch. Unless working in a team and branch is protected, assign reviewers to PR, wait for approval, then merge (or team will merge).

Loop to Step 1.

**Integrate with JIRA**
* Follow the integration docs [here](https://support.atlassian.com/jira-cloud-administration/docs/integrate-with-development-tools/) and integrate with Github [here](https://support.atlassian.com/jira-cloud-administration/docs/integrate-with-github/)
* The data is automatically linked between JIRA and the repo:
    * If the issue key is in the commit message
    * If the issue key is in the branch name
    * If the issue key is in the PR title or branch name
    * If the issue key is in the title of the review
* The issue key must have the syntax
    * Exact issue key with uppercase letters a dash and numbers.
    * For example `ABC-123`
* When making a commit follow this template:
    * `git commit -m "ABC-123 added awesome feature`
    * `git push origin <branchname>`

**Protect main branch**
* See best practices for protected branches [here](https://github.community/t/best-practices-for-protected-branches/10204) and [here](https://cityofaustin.github.io/ctm-dev-workflow/protected-branches.html).
* When working in a team:
    * Settings/Branches/Branch protection rules/Add rule
    * Type `main`
        * Require pull request before merging
            * Require approvals
            * Dismiss stale pull request approvals when new commits are pushed
        * Include administrators
        * Create rule/Save changes
    * Go back to Settings/Branches to confirm the rule was created
* When working solo:
    * Settings/Branches/Branch protection rules/Add rule
    * Type `main`
        * Require pull request before merging
        * Include administrators
        * Create rule/Save changes
    * Go back to Settings/Branches to confirm the rule was created

**Create a branch with the JIRA issue ID**
* Follow [this](https://dev.to/suprabhasupi/github-branch-naming-convention-2d4n) naming convention when creating branches `JIRAid/short-description`
* Example: `git checkout -b ABC-123/add-login`

**Make changes on branch**
* Iterative commits that are easy to revert back

Check that you are in main branch

    $ git checkout main

Check that you have the latest updates before start working

    (main)$ git pull
    (main)$ git checkout -b ABC-123/add-login

Make changes on branch and push branch:

    (ABC-123/add-login)$ touch main.css
    (ABC-123/add-login)$ git commit -m "ABC-123 added css file"
    (ABC-123/add-login)$ echo "Something" > awesome.txt
    (ABC-123/add-login)$ git commit -m "ABC-123 added awesome"
    (ABC-123/add-login)$ git push -u origin ABC-123/add-login

**Create a PR**

When work branch is completed

* Go to the Github repo.
* Go to tab `Pull requests`
* Find your branch and click `Compare & pull request`
* It shows `Open a pull request`
    * Enter new `title` with naming convention JIRA issue id and branch description such `ABC-123 added login box` and `comment` with a template as shown below
    * `base` should be `main`
    * `compare` should be the branch
    * When working with a team
        * Add `Reviewers` on the right
        * Click button `Create pull request`
    * When working solo
        * Click button `Create pull request`

PR templates:

* Kubernetes repo example:
    * A random PR from the Kubernetes repo [here](https://github.com/kubernetes/kubernetes/pull/107683) has these sections in the body of the PR, although there is no indication that they use JIRA:
        * Type of PR
        * What the PR does/why we need it
        * Which issues this PR fixes (link to issue)
        * Special notes for your reviewer
        * Does this PR introduce a user-facing change?
        * Additional documentation/usage docs.
* Hyperledger fabric repo example:
    * A random PR from the repo [here](https://github.com/hyperledger/fabric/pull/2997) has this body:
        * Type of change
        * Description
        * Additional details
        * Related issues (contains JIRA issue ID)
    * This PR [here](https://github.com/hyperledger/fabric/pull/2185) has the issue ID on the PR title.
        * Fabric issues on JIRA [here](https://jira.hyperledger.org/projects/FAB/issues/FAB-18478?filter=allopenissues)
        * And [here](https://jira.hyperledger.org/browse/FAB-18363) is the issue ID mentioned on the PR title.

**Merge PR: working with a team**
* After PR is reviewed, possibly updated, and approved
* Merge PR unless the branch is protected.

**Merge PR: working solo**

* Pull request confirmation
    * Resolve conflicts or
    * `Merge pull request` if it says `This branch has no conflicts with base branch`
* Confirm merge
    * Click `Confirm merge`

**Delete branch**
* After PR is merged, delete the story branch completed
* Click `Delete branch`

## Branching model (Not using this)

Here is the branching model that I am not using anymore. But it could be good for software that needs version maintenance.

I am following this simple version for branches:

* Master
* Develop
* hotfixes

I start in `main`.

Create a branch `develop` to build everything.

When the tests pass or at least most of them, then switch to `main` and merge `develop`.

Create a `hotfix` branch to resolve tests that didn't pass or things I broke. After merging them to `main` and `develop`. The hotfix branches are deleted.

Using a `CHANGELOG` to bump the versions merging `develop` to `main` and `hotfix` to `main`.

I follow the "keep a changelog" tutorial [here](https://keepachangelog.com/en/1.0.0/) and a light semantic version from the Semver website [here](https://semver.org/). Using the `Major.Minor.Patch` syntax.

I use `Sublime` or `vim` for scripts or small projects. Lately I use `IntelliJ Ultimate` which I find amusing with the auto completion, shortcuts, and the `git diff` tool to see changes.

The [Pro Git](http://git-scm.com/book/en/v2) is a great resource to learn Git in more detail.

## Working with main and develop

Initialize the repo with `git init` or clone the repo.

Create the `CHANGELOG` file with `touch CHANGELOG.md`.

Create the `develop` branch `git branch develop`.

Work on the `develop` branch.

Commit in small increments

    (develop)$ git commit -am "Short summary of change"

Switch to main and merge

    (main)$ git checkout main
    (main)$ git merge develop -m "Short summary of merge"

Update `CHANGELOG` file with next version `<version number>`. Then commit:

    (main)$ git commit CHANGELOG.md -m "Bumped version to <version number>"

Create an annotated tag for the `<version number>`.

    (main)$ git tag -a <version number>

Push changes to remote if needed:

    (main)$ git push origin main


## Creating hotfixes of failed tests or things I messed up

Create a branch from `main` increasing the `patch` version number of `Major.Minor.Patch`. If the current version is `1.2` then:

    (main)$ git checkout -b hotfix-1.2.1 main

Update the `CHANGELOG` to increase the version then commit.

    (hotfix-1.2.1)$ git commit CHANGELOG.md -m "Bumped version number to 1.2.1"

Make the changes and commits in small increments.

Then merge with `main` and tag

    (main)$ git checkout main
    (main)$ git merge --no-ff hotfix-1.2.1
    (main)$ git tag -a 1.2.1

Also merge with `develop`

    (main)$ git checkout develop
    (develop)$ git merge --no-ff hotfix-1.2.1

Then delete the branch

    (develop)$ git branch -d hotfix-1.2.1

***

Other Git lessons learned...

## Initialize a Git repository

Initializing a git repo and creating a branch without committing to main causes this strange behavior. Also reported in [this](https://programmer.help/blogs/pathspec-master-did-not-match-any-files-known-to-git.html) blog post.

    $ git init
    $ git status
    On branch main

    No commits yet

    $ git checkout -b develop
    $ git status
    On branch develop

    No commits yet

    $ git checkout main
    error: pathspec 'main' did not match any file(s) known to git

To avoid this. Make a first commit to main then create a branch

    $ git init
    $ touch .gitignore
    $ git add .
    $ git commit -m "Added gitignore"
    [main (root-commit) 98c55ef] Added gitignore
    $ git branch -M main
    (main)$
    $ git checkout -b develop
    (develop)$
    $ git checkout main
    Switched to branch 'main'
    (main)$
    $ git branch
      develop
    * main

## Other Git/Github tutorials I wrote:

* Setup a Github lab for [learning Github](../learning-github/).
* How to [list developers on a Github repo](../list-developers-github-repository/)
* Setup a [custom domain in Github pages](../github-pages-custom-domain/)
* Cheat sheet for [Jekyll build and deploy to Github pages](../jekyll-build-deploy-github-pages/)


## My take on the Git cheat sheet

* Set your email for all repos `git config --global user.email "your email"`
* Set your user name for all repos `git config --global user.name "your name"`
* Set your email and user name for a specific repo. Do the same but remove the `--global` flag.
* Using short status: `git status -s`
* Setup `.gitignore`
* Use `git diff` to see staged/unstaged changes or use an IDE
* Removing files from Git `git rm <filename>`
* Rename files `git mv <filename>`
* View the commit history `git log`
* Last commit `git log -1`
* Ammend the last commit to change the comment `git commit --amend`
* Log in one-liner `git log --pretty=oneline`
* Show the last 4 commits `git log --pretty=oneline -4`
* View the URLs of the remote `git remote -v`
* Fetch data from origin `git fetch origin`
* Merge remote with local branch `git pull`
* In `git push origin main` the word `origin` is just a shortname for the remote. You can rename to `homersimpson` if you want.
* Show more info about a remote `git remote show origin`
* Rename a remote `git remote rename origin to homersimpson`
* Check the remote again `git remote -v` shows `homersimpson https://somerepo`
* Show the number of tags `git tag | wc -l`
* With `git tag` show all the tags
* Show only those that start with `v2`: `git tag -l "v2.2*"`
* Show the details of a tag `git show <version number>`
* Create a `lightweight` tag: `git tag <version number>`
* Create an `annotated` tag: `git tag -a <version number>`
* Create a shortcut `alias` like this: `git config --global alias.last5 'log --pretty=oneline -5'`
* Then run `git last5` to get the same output
* Create a branch `git branch newBranch`
* Switch to that branch `git checkout newBranch`
* Create a branch and switch to it in one command `git checkout -b anotherBranch`
* List local branches `$ git branch`
* History of commits `git log --all --decorate --oneline --graph`
* Last commit in each branch `git branch -v`

## Add Git branch to the command prompt

Edit `.bashrc` as seen on stackoverflow [here](https://stackoverflow.com/a/35218509/1434022):

    # Git branch name
    parse_git_branch() {
            git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/(\1)/'
    }

Add this to `.bashrc` after the above function. The enclosed `\e[0;31m ....\e[m` will set the command prompt to red.

    PS1="\e[0;31m\h\$(parse_git_branch)\$ \e[m"

## Merging conflicts

When doing `git merge`, the output might say `CONFLICT: Merge conflict in <filename here>`

Open the conflict file manually and correct the conflict.

If the file shows something like this:

    <<<<<< HEAD changes2.md
    Updated changes2
    ================
    Finished feature2
    >>>>>>> feature2:changes2.md

Then the main branch has the content `Updated changes2` and the feature2 branch has `Finished feature2`. Remove any Git notes and leave the correct changes such as:

    Updated changes2
    Finished feature2

Then commit the conflict:

    (main)$ git add changes2.md
    (main)$ git commit -m "Merge conflict fixed for feature2"

More about merge tools:

* Use an IDE
* Using `git mergetool` or advanced merging in the Git book [here](http://git-scm.com/book/en/v2/ch00/_advanced_merging)
* Find tools available `git mergetool --tool-help`
* Using `vimdiff` tutorial [here](https://gist.github.com/karenyyng/f19ff75c60f18b4b8149)


## Git rebase

More about rebase [here](http://git-scm.com/book/en/v2/Git-Branching-Rebasing)

## See everything done in git with git reflog

Use `git reflog` as seen on the Dangit Git tutorial [here](https://dangitgit.com/en)

    (main)$ git reflog
    aaaf830 (HEAD -> main) HEAD@{0}: commit (merge): Merged conflict for cities
    32a6bb7 HEAD@{1}: checkout: moving from newBranch to main
    37f3122 (newBranch) HEAD@{2}: commit: Added New Orleans to Cities
    5cb15fc HEAD@{3}: checkout: moving from main to newBranch
    32a6bb7 HEAD@{4}: commit: Added Houston to Cities
    f4b7070 HEAD@{5}: commit (merge): Merge conflic fixed
    3c897b2 HEAD@{6}: checkout: moving from newBranch to main
    e567c27 HEAD@{26}: commit: Deleted names.txt
    a715798 HEAD@{27}: commit: Added a city to cities
    a1b0314 HEAD@{28}: commit (initial): Created files names and cities

Go back in time to a previous commit with this syntax:

    $ git reset HEAD@{index}


## Committed to the wrong branch

Commited to the wrong branch `main`:

    (main) $ echo "England" >> countries.csv
    (main) $ git commit -am "Added England to Countries"

Check the log:

    (main) $ git last5

    4c769 (HEAD -> main) Added England to Countries
    aaaf8 Merged conflict for cities
    37f31 (newBranch) Added New Orleans to Cities
    32a6b Added Houston to Cities
    f4b70 Merge conflic fixed

Use `git reset HEAD~ --soft` to remove the last commit from `main` but keep the changes in the working directory.
    
    (main) $ git reset HEAD~ --soft

    (main)$ git last5
    aaaf8 (HEAD -> main) Merged conflict for cities
    37f31 (newBranch) Added New Orleans to Cities
    32a6b Added Houston to Cities
    f4b70 Merge conflic fixed
    5cb15 Added Rome to Cities

    (main)$ git status
    On branch main
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
            modified:   countries.csv

Use `git stash` to save the state of the working directory and index. More about git stash in the official docs [here](https://git-scm.com/docs/git-stash).

    (main) $ git stash

    Saved working directory and index state WIP on main: aaaf830 Merged conflict for cities

    (main)$ git status
    On branch main
    nothing to commit, working tree clean

Switch branches and use `git stash pop` to remove the last state from the stash list and apply it to the current branch.

    (main) $ git checkout -b addEngland
    Switched to a new branch 'addEngland'

    (addEngland) $ git stash pop
    On branch addEngland
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   countries.csv

    (addEngland)$ cat countries.csv 
    France
    England

    (addEngland)$ git commit -am "Added England to Countries"
    [addEngland 99f82cb] Added England to Countries
     1 file changed, 1 insertion(+)

Check the log in the branch:

    (addEngland)$ git last5
    99f82 (HEAD -> addEngland) Added England to Countries
    aaaf8 (main) Merged conflict for cities
    37f31 (newBranch) Added New Orleans to Cities
    32a6b Added Houston to Cities
    f4b70 Merge conflic fixed

Go to main branch

    (addEngland)$ git checkout main
    Switched to branch 'main'

    (main)$ git last5
    aaaf8 (HEAD -> main) Merged conflict for cities
    37f31 (newBranch) Added New Orleans to Cities
    32a6b Added Houston to Cities
    f4b70 Merge conflic fixed
    5cb15 Added Rome to Cities

## Collaborating with others in Github

If you are the admin. Lock the main branch so that others cannot push directly to main.

**Best Practice**

* Don't commit directly to `main`
* Make code changes on a branch
* Push branch to central repo
* With Github GUI merge using a Pull Request

**Benefits**

* Prevents from doing a `force push` to main
* Allows code reviews of pull request before merging

**Working with Git branches**

Check that you are in main branch

    $ git checkout main

Check that you have the latest updates before start working

    (main)$ git pull

Create a branch and name it using your repo's convention

    (main)git checkout -b awesome-branch

Work on the branch, commit and push to the branch

    (awesome-branch)$ git add .
    (awesome-branch)$ git commit -am "Commit message"
    (awesome-branch)$ git push -u origin awesome-branch

When work branch is completed

* Go to team's Github repo.
* Go to tab `Pull requests`
* Find your branch and click `Compare & pull request`
* It shows `Open a pull request`
    * Enter new (or default: from last commit) `title` and `comment`
    * `base` should be `main`
    * `compare` should be the branch
    * Add `Reviewers` on the right
    * Click button `Create pull request`

If **you can merge your own** pull request

* Pull request confirmation
    * Resolve conflicts or
    * `Merge pull request` if it says `This branch has no conflicts with base branch`
* Confirm merge
    * Click `Confirm merge`
* Click `Delete branch`

If **you cannot merge your own** pull request

* Add team member to merge your pull request
* That team member can add themselves as a reviewer and approve the PR

After PR has been approved and merged. Then update `main` in local repo

    (awesome-branch)$ git checkout main
    (main)$ git pull


## Merge main with local branch when main was updated

As seen on Stackoverflow [here](https://stackoverflow.com/a/4556531)

If you are working on a local branch. Then main was updated. How do you update main and merge to local branch?

Say that the branch is called `develop` and some changes were made.

    (main)$ git checkout -b develop

Then commit the changes

    (develop)$ git commit -am "Awesome comment here"

Then pull changes from main into branch. One of the answer says that rebase is used only for your own local branches and not when the branch is shared with others. This will make above commit ahead of updated main into branch.

    (develop)$ git pull --rebase origin main


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)