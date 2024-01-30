# Fixups

In a PR review, some feedback about Commit Two requires making some changes. After the changes have been made, we can fixup Commit Two to avoid extra commits. First we need to grab the reference.

```shell
git log
commit fa11fa11fa11fa11fa11fa11fa11fa11fa11fa11
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 15:09:29 2024 -0600

    Commit Three

# copy this commit has (or first 7)
commit facefacefacefacefacefacefacefacefaceface
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 09:44:37 2024 -0600

    Commit Two

commit cafecafecafecafecafecafecafecafecafecafe
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Fri Jan 26 15:09:01 2024 -0600

    Commit One
```

Now we can use the --fixup flag to update an existing commit

```shell
git commit --fixup a9d3dea #updating Commit Two
```

Just to confirm here is the new log

```shell
git log
# the commit message is prefixed with fixup! which is how git figures
# out where to merge this in the next step
commit beeffeedbeeffeedbeeffeedbeeffeedbeeffeed
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 15:09:29 2024 -0600

    fixup! Commit Two

commit fa11fa11fa11fa11fa11fa11fa11fa11fa11fa11
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 15:09:29 2024 -0600

    Commit Three

commit facefacefacefacefacefacefacefacefaceface
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 09:44:37 2024 -0600

    Commit Two

commit cafecafecafecafecafecafecafecafecafecafe
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Fri Jan 26 15:09:01 2024 -0600

    Commit One
```

Now, we can perform a rebase to combine our fixup commit with Commit Two

```shell
# we can use ~1 to mean the commit before this commit
git rebase -i --autosquash a9d3dea~1

This will open the text editor you have configured with the key core.editor

pick cafecafec Commit One
pick facefacef Commit Two
fixup beeffeedb fixup! Commit Two
pick facefacef Commit Three

# Rebase baseba119..beeffeed6 onto baseba119 (2 commands)
#
# Commands
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
# commit's log message, unless -C is used, in which case
# keep only this commit's message; -c is same as -C but
# opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom
#
# If you remove a line here THAT COMMIT WILL BE LOST
#
# However, if you remove everything, the rebase will be aborted
```

Now, we have rewritten our git history as if our fixup changes were part of the original set of change.

```shell
git log
commit fa11fa11fa11fa11fa11fa11fa11fa11fa11fa11
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 15:09:29 2024 -0600

    Commit Three

# note: this commit hash has changed
commit ba11ba11ba11ba11ba11ba11ba11ba11ba11ba11
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Mon Jan 29 09:44:37 2024 -0600

    Commit Two

commit cafecafecafecafecafecafecafecafecafecafe
Author: Robert Masen <r.f.masen@gmail.com>
Date:   Fri Jan 26 15:09:01 2024 -0600

    Commit One
```

I personally have this script in my path named git-fixup

```sh
# ! /bin/bash
# ~/.local/bin/git-fixup
# this can be used via `git fixup <commit-hash>`
git commit --fixup $1 && git rebase -i --autosquash $1~1
```
