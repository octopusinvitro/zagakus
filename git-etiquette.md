# Git etiquette

> ["The biggest why for git's existence is telling the story of your project"](https://www.youtube.com/watch?v=Dv8I_kfrFWw)<br>
> &mdash; Jessica Kerr

Having a clean git history is just [as important as writing clean code](https://fangpenlin.com/posts/2013/09/30/keep-a-readable-git-history/).


## Git good practices


* Commits should have a single responsibility.

* The commit message should use verbs in imperative tense (for example: `Add README`, instead of `Adds README` or `Added README`).

* Commits should explain the "*why*" rather than the "*what*".

* It's a good idea to organize the message into a summary line and a longer body
  - Keep the summary line clear and concise. Ideally it should not be more than 50 characters.
  - Keep the body focused. Wrap the body lines around ~70 characters.


* Don't litter the project's history with unnecessary merge commits. When pulling new commits when you have local commits that haven't been pushed yet, do `git pull --rebase` rather than just `git pull`.

* **Clean history 1:** It's a good practice to rebase the branch with master just before merging the branch into master. For that, use `git rebase master` on the child branch, then merge. This will produce a fast-forward merge.

* **Clean history 2:** Same applies to merging other branches into their parent branches. For that, use `git rebase PARENT_BRANCH` on the child branch, then merge. This will produce a fast-forward merge.

* **Clean history 3:** If you want git to maintain a history of featured branches, you can always avoid fast-forward merges by adding a  merge commit. For that, use `git merge --no-ff` instead of `git merge`, after rebasing.

* Use `rebase` thoroughly to clean your history. Do not submit commits to the project that are litter or fix typos. Instead, squash, split and reorder commits as necessary. Always use `rebase` to update a child branch with its parent, rather than using `merge`.

* When rebasing, don't change any commit from the parent branch, only commits from the child branch. Parent branch commits should be treated like master commits. To see the commits that belong just to the child branch use `git log PARENT_BRANCH..CHILD_BRANCH`

* Once a branch is merged into master, there is no point in leaving it hanging, creating confusion. Delete the branch locally and remotely. You can do this easily by adding this alias to your `.bash_aliases`:
```bash
delete() {
  git push origin :$1
  git branch -D $1
}
```
then you can use it like this: `delete BRANCH_NAME`


## A guide to professional pull requests

* Always work on tiny branches. Small PRs are easier to review, and are more likely to be merged fast.

* It's a good habit to do "cleaning" PRs before the real PR, to fix the code first for the change we want to do.
> ["_Make the change easy, then make the easy change_"](https://twitter.com/kentbeck/status/250733358307500032?lang=en).<br>
> &mdash; Kent Beck

* Before you submit a PR, remember to check if the README or the documentation needs updating after your change.

* PRs are not the place to comment on syntax, whitespace, etc. Agree with the team beforehand on the conventions to be used, and use a linter to automatically enforce consistency in the codebase.

* Make sure the code you submit with your PR is covered by tests, and that all the tests pass. Branches should not be merged if tests aren't green.

* PR templates are a useful tool to remind contributors of things that should be checked before submitting a PR.

* When addressing feedback on a PR that requires modifying a change introduced by a commit in that branch, don't add a new commit. Use `git commit --fixup COMMIT_SHA`. Then, before merging, you can squash those commits with `git rebase -i --autosquash PARENT_BRANCH`.

* The contributor who submitted the PR is usually the one who merges the branch after the PR has been reviewed and approved. This is because branches should be rebased before merging (clean history approach), and the contributor knows better how to fix conflicts if they appeared.

* It is recommended that every single commit passes the tests in isolation. An example of when this could be useful is if you need to do `git bisect` at some point. For example, if you are working on a ruby project that uses rake to run the tests, you can check that every commit passes the tests using:

```git
git rebase -i --exec 'bundle exec rake' master
```

The rebase will stop if tests fail. Then you can fix them and do `git rebase --continue` when you are done.


## Tips and tricks

* Want to add only bits of a file? (works also for `checkout`, `reset`, `stash`, etc.)

```git
git add file -p
```

* Same but file is new and still not tracked?

```git
git add file -N
git add file -p
```

* Want to copy the same commit message than another commit with `COMMIT_SHA`?

```git
git commit -c COMMIT_SHA
```

* Want to have GitHub-like word by word difference highlights?

```git
git diff --color-words='.'`
# another variant:
git diff --word-diff
```

* Want to gitignore something without adding it to `.gitignore`?

Add it to `.git/info/exclude` instead.

* Discovered a typo 5 commits behind? Don't add a commit that says 'Fixes typo'. Instead:

```git
git add FILE_AFTER_CORRECTING_TYPO
git commit --fixup COMMIT_SHA_OF_FILE_WITH_TYPO
git rebase -i --autosquash PARENT_BRANCH
```

* Same but you added some other changes to the same file after that commit? Just do an interactive rebase and edit the commit:

```git
git rebase -i PARENT_BRANCH
# change commit of file with typo to 'edit' and fix typo
git rebase --continue
```

* Want to stash everything, including untracked files?

```git
git stash -u
```

* Want to stash but not the files that are in staging?

```git
git stash --keep-index
```

* Want to run a command for all the commits in the branch? For example, running the tests?

```git
git rebase -i --exec 'COMMAND_TO_RUN_THE_TESTS' PARENT_BRANCH
```

Beware: your bash aliases don't work here! Type the whole command.

* Want to pull the current branch but you don't know if there are new commits upstream and you have new local commits?

```git
git pull --rebase origin CURRENT_BRANCH
```

This is because `pull` is a `fetch` followed by a `merge`. Alternatively:

```git
git checkout SOME_OTHER_BRANCH`
git fetch
git checkout THE_BRANCH
git rebase origin/THE_BRANCH
```

* When you don't like to edit your commits in an editor (like sane humans do), but you still want to have a message and a body:

```git
git commit -m 'Message here' -m 'Long body description here, but you gotta be a masoquist to do this'
```

* Want to show just the commits of the current branch?

```git
git log PARENT_BRANCH..CURRENT_BRANCH
```

* Want to show just the git history of a file?

```git
git log FILENAME
```

* Same but for a single function in a file?

```git
git log -L :FUNCTION_NAME:FILENAME
```

* Want to split a commit in two?

```git
git rebase -i PARENT_BRANCH
# mark commit as 'edit'
git reset HEAD^
# this reset undoes the changes, so now you can commit them separately
```

* Want to amend the author of all commits in this branch?

```git
git rebase -i --exec 'git commit --amend --author "New Author Name <email@address.com>"' PARENT_BRANCH
```

alternatively, if you have already set the new name and email locally or globally:

```git
git rebase -i --exec 'git commit --amend --reset-author' PARENT_BRANCH
```

* Want to push to several upstream repos at the same time?

Say you have 3 origins called `github`, `bitbucket` and `gitlab`:

```git
[remote "github"]
  url = git@github.com:octopusinvitro/zagakus.git
  fetch = +refs/heads/*:refs/remotes/github/*
[remote "bitbucket"]
  url = ssh://git@bitbucket.org/octopusinvitro/zagakus.git
  fetch = +refs/heads/*:refs/remotes/bitbucket/*
[remote "gitlab"]
  url = git@gitlab.com:octopusinvitro/zagakus.git
  fetch = +refs/heads/*:refs/remotes/gitlab/*
```
Open your `.git/config` file and add:

```git
[remote "origin"]
  url = git@github.com:octopusinvitro/zagakus.git
  url = ssh://git@bitbucket.org/octopusinvitro/zagakus.git
  url = git@gitlab.com:octopusinvitro/zagakus.git
```

Now when you do `git push origin` you will push to the three at once. You will still have to pull from each separately though.


## Resources

* [Commit messages, by Tim Pope](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
* [Getting solid at Git rebase vs. merge](https://medium.com/@porteneuve/getting-solid-at-git-rebase-vs-merge-4fa1a48c53aa)
