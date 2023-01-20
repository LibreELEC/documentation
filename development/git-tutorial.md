# Beginners Guide to Git

Most community/user developers have simple git goals, for example:

* Changing the Linux kernel `defconfig` to enable a driver
* Bumping the version for a software or binary add-on package
* Contributing to project documentation!

The goal of this page is to explain essential git workflow and terms. Git is mind-bendingly ~~complicated~~ clever and there are hundreds of tricks to learn, but the basics are logical and quite simple to pick-up. One of the good things about git is; everyone is still learning! and everyone remembers what it was like to be a git n00b at the beginning. If you need to ask questions, ask them, as people are happy to coach.

## Forking and Cloning

The project maintains its primary git repository (repo) at [https://github.com/LibreELEC/LibreELEC.tv/](https://github.com/LibreELEC/LibreELEC.tv/). The repo hosts the `master` development branch where the next release takes shape, and stable release branches, e.g. `libreelec-9.2` and `libreelec-9.0` that accumulate release-specific changes (commits) over time.

To self-build an image and test something you can simply `git clone` our sources and start building, but if you want to maintain personal customisations and changes over time it's better to `fork` our sources to your own git repo. Having your own fork makes the process of storing, sustaining, and sharing your changes easier. Forking is done online by visiting the LibreELEC repo and then pressing the fork button (top right of the screen). GitHub will make a copy of our sources under your own user account. Now instead of cloning OUR sources you `git clone` a copy of YOUR sources to your local hard-drive.

```
git clone https://github.com/username/LibreELEC.tv
cd LibreELEC.tv
```

## Origin and Upstream

Like ours, your fork will have a `master` branch and release branches like `libreelec-9.2` which contain a sequence of commits. When you clone your repo to your local drive, git automatically creates references to your copy on GitHub which is the `origin` of your local sources. To update your local branches with changes from our `upstream` branches, we will define a `remote` repo. You can add many remote(s), but we will add just one:

```
git remote add upstream https://github.com/LibreELEC/LibreELEC.tv.git
```

## Topic Branches

One of the most important principles of git is **NEVER WORK IN THE MASTER BRANCH !!**. To make a one-time change then build an image it's fine, but if you make changes in branches that we maintain (upstream) it becomes complicated to maintain your local (downstream) changes. It's best to keep your changes in a separate "topic" branch. To create a new topic branch, you `checkout` a new branch from the one you want to modify. For example, to modify the `libreelec-9.2` branch, we switch to it, then create the "mychanges" topic branch:

```
git checkout libreelec-9.2
git checkout -b mychanges
```

## Commit Changes

In git-speak when you save your work you `commit` changes to the working (topic) branch. The mantra for committing is "commit early, commit often" because it's always simple to combine (squash) commits together at a later stage, but hard to split a large commit into many smaller pieces. Commits can document changes to a single file or a collection of files, with a readable "commit message" that describes the changes. LibreELEC has a "house style" for commit messages: `tag: summary of changes made` where `tag` is the name of the package being changed or a functional area of the build system and `summary of changes made` explains in brief what the change does. It helps to make them descriptive so you can read the `git log` later and understand which commit changed something. For example, if we enable an extra driver or module in the Linux kernel:

```
nano projects/Generic/linux/linux.x86_64.conf
```

Make changes and save the file:

```
git add projects/Generic/linux/linux.x86_64.conf
git commit -m "linux: enable RTL8822CE wireless driver"
```

Most users will make changes first, then build and test them, then `commit` the changes.

## Fetching and Rebasing

Over time staff and users contribute changes to our `upstream` branches and your local fork falls behind. To update your "mychanges" topic branch you must first commit any unsaved changes, then switch back to the branch it was based upon, e.g. the `libreelec-9.2` branch:

```
git checkout libreelec-9.2
```

Next we need to `fetch` the latest changes from the `upstream` branch to our local cache, then reset the local copy of the branch to match the upstream one:

```
git fetch upstream libreelec-9.2
git reset --hard upstream/libreelec-9.2
```

Now the local `libreelec-9.2` branch contains the latest changes we can change back to our `mychanges` topic branch and `rebase` the branch. Rebasing re-applies the single or handful of changes that you made (and committed) to the branch you are rebasing against, e.g.

```
git checkout mychanges
git rebase libreelec-9.2
```

If you look at the log of commits you will see the `mychanges` branch matches the upstream `libreelec-9.2` branch, with your local changes applied on top.

```
git log --pretty=oneline
```

## Interactive Rebasing

This is the git trick that everyone needs to learn. It is often the "Eureka!" moment that moves a user from novice to normal. Interactive rebasing allows you to (re)edit, remove, and reorder the sequence of commits in the branch. The reasons you will want/need to do this are dropping a change, grouping commits together so the sequence is easier to understand, to edit (reword) one or more commit messages, or to combine (squash or fixup) multiple small changes into a single larger commit. Interactive rebasing is done to a specific number of commits measured from the top (HEAD) of the branch, e.g. to rebase the last five changes:

```
git rebase -i HEAD~5
```

The list of commits will open in a text editor, e.g. "nano" and you can cut (ctrl+k) and paste (ctrl+u) the commits to reorder them. For example, lets say we have the following list which shows which commits are being pick(ed) to the branch:

```
pick 73d9356c36 fix typos in file A
pick 29de6de78b add new file B
pick be444ce9e7 more typos in A
pick cf7149bc7e more typos in A
pick 750b824c04 changes to file B
```

It would be more logical to group all the `file A` and `file B` commits like this:

```
pick 73d9356c36 fix typos in file A
pick be444ce9e7 more typos in A
pick cf7149bc7e more typos in A
pick 29de6de78b add new file B
pick 750b824c04 changes to file B
```

The next logical step might be to combine the changes so there are only two commits; one for changes to file A and one for changes to file B. This is done by changing `pick` to `squash` or `fixup` which combine commits. In the sequence below, the first commit is at the top, and we want to combine the next two minor changes into it. If you `squash`commits you will be asked to edit the commit messages (choose which of the commits should be used for the message) while `fixup` combines the lower (second) commit into the upper (first) commit and uses the first commit message without prompting. For example:

```
pick 73d9356c36 fix typos in file A
fixup be444ce9e7 more typos in A
fixup cf7149bc7e more typos in A
pick 29de6de78b add new file B
fixup 750b824c04 changes to file B
```

Once you save (ctrl+o) and exit edit mode (ctrl+x) the reordering and squash/fixup changes are applied, leaving you with two commits for "fix typos in file A" and "add new file B" in the branch. It is a good habit to squash and rebase frequently to keep your changes logically grouped and in a minimum number of commits. This is important if you want to send your changes upstream in a "pull request"  to our repo. Project staff want to review a small and logical set of changes, not the monster list of nip-tuck changes you needed to figure out what changes were needed.

## Push and Force-Push

Another good habit is `pushing` your local changes back to your `origin` branch on GitHub. If something gets messed up in your local branch (and when you are starting out with git, stuff will get messed up!) you can always reset your local branch to the previous known-good state stored on GitHub. Pushing the changes is simple:

```
git push origin mychanges
```

If you have rebased the `mychanges` branch the branch history will have different references and a force-push will be needed:

```
git push -f origin mychanges
```

There are also no restrictions on the number of branches you can make. So fork branches and push them upstream. It's better to fork and store changes and then tidy things up occasionally than lose changes and have to repeat/reinvent them.

## Pull Requests (PR) <a href="#pullrequests" id="pullrequests"></a>

If your changes might benefit the project and you'd like to see them included `upstream` in our repo so you don't need to maintain them in your `downstream` fork, you'll need to submit a `pull request` (referred to as a PR.) This is done in the GitHub interface. You need to push your changes to your origin branch, and then when you view the code on GitHub, select the "pull request" button. You'll be prompted to select the repo/branch to send the request to, e.g. `LibreELEC.tv/master` and you'll give the request a name and description. If your pull request modifies code, please include a good description of the problem that your changes solve or what the feature is that you added, and (important!) the testing you conducted. So called "blind requests" where there is no evidence of testing and no meaningful explanation of the change, are rarely merged (or merged quickly) into our codebase.&#x20;

Note: All functional changes should be submitted to the `master` branch first, even if the target for your changes is the current stable release branch. This way the branches remain in sync, and we don't have orphaned features.
