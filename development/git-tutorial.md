# Git Tutorial

Most LibreELEC users have simple git goals, for example:

- Changing the Linux kernel `defconfig` to enable a driver
- Bumping the version for a software or binary add-on package
- Contributing to project documentation!

The goal of this page is to explain some essential git workflow and terms. Git is mind-bendingly ~complicated~ clever and there are hundreds of tricks to learn, but the basics are logical and simple to pick-up. One of the good things about git is; everyone is still learning! and everyone remembers what it was like to be a git beginner. So if you have questions, ask them, as people are happy to coach.

## Forking and Cloning

The project maintains its primary git repo (repository) at [https://github.com/LibreELEC/LibreELEC.tv/](https://github.com/LibreELEC/LibreELEC.tv/). The repo contains the `master` development branch where the next major release takes shape, and a number of stable release branches, e.g. `libreelec-9.2` and `libreelec-9.0` that accumulate release-specific commits (changes) that accumulate over time.

If you want to self-build an image to test something you can simply `git clone` our sources and start building, but if you want to maintain customisations and changes over time it's better to `fork` our sources to your own git repo. Having your own fork makes the process of storing, sustaining, and sharing your changes easier. Forking is done online by visiting the LibreELEC repo and then pressing the fork button (top right of the screen). GitHub will make a copy of our sources under your own account. Now instead of cloning OUR sources you `git clone` a copy of YOUR sources to your local hard-drive.

```
git clone https://github.com/username/LibreELEC.tv
cd LibreELEC.tv
```

## Origin and Upstream

Your fork will have a `master` branch and release branches like `libreelec-9.2` which contain a sequence of commits. When you clone your repo to your local drive, git automatically creates references to the `remote` online (GitHub) version, which is the "origin" of your local copy. To update your local branches with changes from our remote branches, we will define an "upstream" repo. You  can add many remote repos (if you need to) but we will add just one:

```
git remote add upstream https://github.com/LibreELEC/LibreELEC.tv.git
```

## Topic Branches

One of the most important principles of git is NEVER WORK IN THE MASTER/RELEASE BRANCH!. If you just want to make a one-time change and build an image it's fine, but if you make changes in "our" branches it becomes hard to maintain your changes. It's best to keep your changes in a seperate "topic" branch. To create a new topic branch, you `checkout` a new branch from the one you want to modify. For example, to modify the `libreelec-9.2` branch, we switch to it, then create "mychanges" as our topic branch:

```
git checkout libreelec-9.2
git checkout -b mychanges
```

## Commit Changes

In git-speak when you save your work you `commit` changes to the working (topic) branch. The mantra for committing is "commit early, commit often" because it's simple to combine (squash) many small commits together into a single larger commit at a later stage, and hard to split a large commit into smaller pieces. Commits can document changes to a single file, or a collection of files, with a readable "commit messasge" that describes the changes. The LibreELEC repo has a "house style" for commit messages: "tag: summary of changes made" where "tag" is the name of the package being changed, or the functional area of the build system. You can use any style you like, but it helps to make them descriptive so you can read `git log` later and understand which commit changed something. For example, if we enabled a kernel driver:

```
nano projects/Generic/linux/linux.x86_64.conf
(make changes and save the file)
git add projects/Generic/linux/linux.x86_64.conf
git commit -m "linux: enable RTL8822CE wireless driver"
```

Now you can test-build the changes!

## Fetching and Rebasing

Over time your local fork branches will fall behind our `upstream` branches; because staff and users will contribute changes to our repo. To update your "mychanges" topic branch, first you commit any unsaved changes, and then switch back to the branch it was based upon; the `libreelec-9.2` branch.

```
git checkout libreelec-9.2
```

Now we need to `fetch` the latest changes from the `upstream` branch to our local cache, then reset the local copy of the branch to match the upstream one:

```
git fetch upstream libreelec-9.2
git reset --hard upstream/libreelec-9.2
```

Now the local libreelec-9.2 branch contains the latest changes, we can change back to our `mychanges` topic branch and `rebase` the branch. Rebasing re-applies the single or handful of changes that you made (and committed) to your local branch, on-top of the branch you are rebasing against, e.g.

```
git checkout mychanges
git rebase libreelec-9.2
```

If you look at the log of commits, you can see the `mychanges` branch matches the upstream `libreelec-9.2` branch, and has your local changes applied on top.

```
git log --pretty=oneline
```

## Interactive Rebasing

The other kind of rebase is an "interactive" rebase. Interactive rebasing allows you to change and reorder the commits in the branch. The main reasons you will do this are to drop a change, to group commits together so the sequence of changes is easier to read, to edit (reword) a commit message, or the most important; to combine (squash) multiple changes together into a single commit. Interactive rebasing is normally done to a specific number of commits measured from the top (HEAD) of the branch, e.g. to rebase the last five changes:

```
git rebase -i HEAD~5
```

The list of commits will open in a text editor, e.g. "nano" and you can cut (ctrl+k) and paste (ctrl+u) the commits to reorder them. For example, lets say we have the following list:

```
pick 73d9356c36 fix typos in file A
pick 29de6de78b add new file B
pick be444ce9e7 more typos in A
pick cf7149bc7e more typos in A
pick 750b824c04 changes to file B
```

It would be more logical to group and reorder the commits as:

```
pick 73d9356c36 fix typos in file A
pick be444ce9e7 more typos in A
pick cf7149bc7e more typos in A
pick 29de6de78b add new file B
pick 750b824c04 changes to file B
```

The next logical step would be to combine the changes. This is done by changing `pick` to `squash` which will combine the commits and prompt you to confirm which commit message to use (the first commit, or the second commit) or `fixup` which squashes the second commit into the first (using the first commits' commit messsage) without prompting. For example:

```
pick 73d9356c36 fix typos in file A
fixup be444ce9e7 more typos in A
fixup cf7149bc7e more typos in A
pick 29de6de78b add new file B
fixup 750b824c04 changes to file B
```

Once you save and exit edit mode the changes are applied, leaving you with two commits for "fix typos in file A" and "add new file B" in the branch. It is a good habit to squash and rebase frequently to keep changes logically grouped and in a minimum number of commits. This is particularly true if you plan to send a pull-request to the LibreELEC repo.

## Push and Force-Push

Another good habit is `pushing` your local changes back to the `origin` branch on GitHub. If something goes wrong with your local branch (and when you are starting out with git, stuff will go wrong!) this means you can always reset your local branch to the previous known-good state stored on GitHub. Pushing the changes is simple:

```
git push origin mychanges
```

Although if you have rebased the `mychanges` branch the branch history will have different references and a force-push will be needed:

```
git push -f origin mychanges
```

## Pull Requests

If your changes will benefit the project, or you'd like to see them included `upstream` so you don't need to maintain them in your `downstream` fork, you'll need to submit a pull request. This is done in the GitHub interface. You need to push your changes to your origin branch, and then when you view the code on GitHub, select the "pull request" button. You'll be prompted to select the repo/branch to send the request to, e.g. `LibreELEC.tv/master` and you'll give the request a name and description. If your pull request is for code, please include a good description of the problem your changes solve or feature added, and the testing you have conducted. So called "blind requests" where there is no evidence of testing and no meaningful explanation for the change, are rarely merged into our codebase. NB: All functional changes must be submitted to the `master` branch first, even if the target for your changes is the current stable release branch. This way the branches remain in sync, and we don't have orphaned features.
