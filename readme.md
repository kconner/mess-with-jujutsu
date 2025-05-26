Let's follow [Steve's Jujutsu Tutorial](https://steveklabnik.github.io/jujutsu-tutorial/).

Since git is its backend, you can use jj without convincing your team to use it too, which makes it even more worth the experiment.

```sh
brew install jj
```

Looks like this tutorial wants to make a Rust app. Let's do that then.

```sh
brew install rustup # and note the PATH update
rustup default stable
```

Just because jj uses git storage doesn't mean you can use git commands. There is no .git folder after `jj git init` here, only a .jj folder.

- `jj st` or `jj status`: git status

There's no index/stage.

> This is a running theme with jj: it gives you fewer tools, but those tools end up having equivalent or even more power than their git counterparts. Because there are fewer tools, there's also less to learn.

Commit hashes are hexadecimal like git. Change IDs distinctly use the end of the alphabet.

- `jj desc` or `jj describe`: Sets a commit message, but doesn't commit yet.

Describing the current commit doesn't affect its hash or change ID.

Adding a file changes the commit hash. Removing it changes it again, but does not change it back to what it was. Neither of these working copy changes affected the change ID.

- `jj new`: Begins a new blank change, leaving the last committed.

After that, jj st now reports no changes, the working copy's description is blank, and the parent is the one we described a moment ago.

I noticed that if you create a file and check jj st, then .gitignore it and check again, it won't be immediately omitted. If I do both changes before running jj st, it will be ignored. So there are tracking changes written when you do some ostensibly read-only commands.

- `jj log`: git log

`@` means the working copy. Not the same as git's HEAD.

> why _should_ you have to create a commit message at the time of creating a commit, and not whenever you feel like it? The same stuff exists, but in more flexible pieces that I can combine together.

- `jj squash` or `jj amend`: Add working copy changes into the parent change. It keeps the change ID but replaces the commit hash.

Wow how do I keep reaching for the thing that comes next in the tutorial before it's introduced?

- `jj squash -i`: TUI for squashing particular changes into the parent.

In the interactive squash UI, space toggles selection and f unfolds a file to show individual changes. They are a tree. q quits, and confirming that aborts.

- `jj abandon`: Throws out all changes in the working copy. A new empty change ID is created.

Squashing defaults to moving changes from the working copy @ to its parent @-, but can be set go from any change to its parent with -r, or from any change to any other change with --from/-f and --into/-t, each of which default to @. So to edit a few changes ago you can just make the change and then jj squash -t thatchange. I bet you can just add -i if you need it piecewise.

> jj squash is more powerful than git add because it can work on any change and its parent, moving stuff between them… Simpler, but more powerful, thanks to orthogonality.

- `jj new -B @`: Makes a new commit before, not after, the current working copy.

At this point, the log shows the current working copy as being in the middle. The child change is still there but is rebased if I change this one. The child change ID also remains the same.

That makes sense with what I obseved about .gitignore and jj st before. The working copy can change freely, so its commit hash isn't decided until the next time jj actually runs and looks at it. At that point it would affect any child commits too.

- `jj edit foo`: Switches to make a paricular change the working copy. Child commits are automatically rebased as changes are first seen by jj, I think.

- `jj next --edit`: Switches the working copy to the first child of the current working copy change. Like edit but you don't have to look up the change ID in the log.

- `jj log -r 'heads(all())'`: Log only branch tip commits.

A revset is a revision set. A revision is a commit. Revsets can be specified in an expression syntax. @ is a basic revset expression indicating the working copy. Change IDs and commit hashes are expressions. A trailing - is an operator taking the parent of its operand. Other operators do things like set intersection and finding all ancestors. And there are named functions too. trunk(), for example, looks for a traditionally named remote main branch and defaults to your own root commit.

- `jj log -r '@ | ancestors(remote_bookmarks().., 2) | trunk()'`: Presents a handy overview of branches.

- `jj new rev-a rev-b`: Make a merge commit.

> Just like we'd pass a parent revision to jj new, we can pass multiple parents, and it just works. No need for a special command.

- `jj undo`: Undo the last jj operation. Nice! Undoing again will just undo the undo though, so:

- `jj op log`: See a history of jj operations, which have IDs.

- `jj op restore op-id`: Restore to the state after an operation.

> We have rebased our commit successfully. But you may have noticed something surprising: @ is still at pzoqtwuv. This actually belies a very deep difference between jj and git that I learned from [Austin Seipp](https://github.com/thoughtpolice), one of jj's maintainers. And here it is:
>
> jj commands primarily operate on the data structures stored in its repository, rather than on the working copy.

- `jj edit @+`: Edit the child of the working copy, same as `jj next --edit` I think.

- `jj bookmark create -r @- main`: Make a local main branch pointed at the parent change.

- `jj log -r 'ancestors(main, 2)'`: See the last two commits on the main branch.

- `jj bookmark set -r @ main`: Point the main branch at the working copy

Generally you'd update the branch bookmark before you push. New local commits can stay flexible in the meantime.

