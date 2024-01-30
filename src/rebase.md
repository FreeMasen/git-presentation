# Manual Rebase

Sometimes when attempting to rebase a complicated set of changes, or maybe when switching base branches it can become very difficult to pull apart the changes. One way to deal with this is to perform a "manual rebase".

To start we need to get the list of commits we are looking to rebase, this will assume our target branch is named develop

```shell
git log --pretty=oneline develop..HEAD > cherry-picks
```

This will add a line to the file ./cherry-picks for each commit that appears in develop but not HEAD. Note: you may need to remove entries from this list especially if you are changing the base branch.

```shell
# ./cherry-picks
ca5eca5eca5eca5eca5eca5eca5eca5eca5eca5e (HEAD -> work-branch) Commit Three
7ac07ac07ac07ac07ac07ac07ac07ac07ac07ac0 Commit Two
babebabebabebabebabebabebabebabebabebabe Commit One
```

Now we can create a temporary branch to keep these commits around and then reset our branch

```shell
git checkout -b tmp/work-branch
git checkout -
git reset --hard origin/develop
```

This will remove all of your changes on your work branch and reset the state to match exactly our target branch. Now we can cherry-pick our commits on top of the base.

```shell
git cherry-pick babebabeb
git cherry-pick 7ac07ac07
git cherry-pick ca5eca5ec
```

In the event that any of these commits have conflicts, you'll need to deal with the merge conflict and use git cherry-pick --continue before moving on to the next commit.

This has essentially performed the work that rebase is supposed to do but manually.
