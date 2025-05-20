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

- `jj new`: commits.

After that, jj st now reports no changes, the working copy's description is blank, and the parent is the one we described a moment ago.

I noticed that if you create a file and check jj st, then .gitignore it and check again, it won't be immediately omitted. If I do both changes before running jj st, it will be ignored.
