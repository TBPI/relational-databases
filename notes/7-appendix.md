# Appendix

## Code Editors

### What is a code editor?

**Code editor =** any application that allows one to edit code.

**Integrated Development Environment (IDE) =** full application that allows one to compile, run, and debug code while editing it.
For example: Visual Studio (by Microsoft), Xcode (by Apple), Android Studio (by Google).

A **cloud-based editor** is an online tool that allows one to write, edit, and manage code or text directly through a web browser without needing to install software locally.
For example, replit, GitHub Codespaces, ONA.

### How to create a project and run the code locally in VS Code?

VS Code considers projects to be `workspaces`.

Some basic bash commands make it easy to work with the terminal.

*Remark.*
Windows PowerShell does not support Bash commands,
but VS Code makes it easy to use Bash
as the integrated terminal.

To do so, create the following file:

`.vscode/settings.json`

If not yet done, install `Git Bash`.

Add the following configuration:
```json
{
  "terminal.integrated.defaultProfile.windows": "Git Bash"
}
```

#### Difference between PowerShell and Bash

These notes are about Bash.
The differences to the Windows PowerShell are huge.

For example to create a new file `new_file.txt`,
the Bash command is
```bash
touch new_file.txt
```
The Windows PowerShell command is
```powershell
New-Item new_file.txt
```

#### Live Server

`mkdir` = make directory.
Creates a new directory

If one creates a file such as `index.html`,
the easiest way to open the file afterwards, is to use the *Live Server* extension.
Then click on *Go Live* or load `http://localhost:5500/` in the browser, to see the live changes.

### Useful keyboard shortcuts for maximizing productivity in VS Code.

`Shift + Alt + F` = runs the configured formatter.

`Ctrl + Shift + F` = search the text contents of all the files in the current workspace.

`Ctrl + Shift + H` = find and replace.

`Ctrl + Shift + K` = delete.

`Ctrl + B` = hide the sidebar.

`Ctrl + Alt + down/up` = extend the cursor to consecutive lines.

`Ctrl + Shift + P` = opens command palette.

## Git

### Working with Nano
Typically Linux environments include Vi or Vim as a text editor. But Vim is quite user-unfriendly.

Editors like Emacs or Nano may need to be installed manually, but are easier to understand.
To open a file, enter `nano <filename>`.
Once the file is open,
one sees the version of Nano running
and the name of the file that is running as well as its content below.

At the bottom there is a list of available keyboard shortcuts, where `^G` (= Help) stands for `Ctrl + G`, and `M-` stands for a combination with the `Alt` key.

To edit a file, press `^O` = `Ctrl + O` in order for the changes being added to the file.

`^C` = `Ctrl + C` allows one to cancel and go back to editing the file.

`^X = Ctrl + X` allows one to exit Nano.

### Version control

Version control system: Git, SVN, Mercurial

These tools make it possible to track
and manage changes in the project.

Usually, a version control system is merged with a **Version control provider**, e.g. GitHub or GitLab.

### Purpose of sites like GitHub / GitLab

A version control provider offers cloud storage for version-controlled projects.

Features of  version control
- boosts collaboration
- open source or private
- branching
- issue tracker (assign, claim, etc.)

### Repositories

When creating a new repository on GitHub,
there are typically the following options:
- *choose a template:* special kind of repository that one can use as a springboard for new repositories (usually not yet set up);
later on useful, e.g. for issue templates or documentation;
- *select an owner:* the private account or an organization;
- *choose a name of the repository:* do not use spaces;
- *choose whether repository is public or private;*

A README file will appear on the repositories homepage: good for documentation.

A `.gitignore` file allows to specify files which should be ignored by Git.

To clone the repository, run in the terminal:
```bash
    git clone git@github.com:username/repository-name
```

### Security Requirements

This chapter deals with handling security requirements such as SSH and GPG keys in the context of GitHub.

Both use public-private pair.
Keep the private key on the personal laptop
and share a public key with people who need to verify the authenticity.

#### GPG
GPG means Gnu Privacy Guard. GPG keys are used to sign files or commits.

To generate a GPG key, need to run (in Bash)

```bash
gpg --full-generate-key
```
One will be asked to provide a passphrase.

The GPG key will be stored in a hidden and encrypted directory.

To see the list of the public GPG keys, run
```bash
gpg --list-secret-keys --keyid-format=long
```

To get the public key, use

```bash
gpg --armor --export "<key id>"
```

As `key id` one can use the e-mail address that is provided when creating the key or the short key id provided after
```bash
sec   rsa3072/KEY_ID
```

Then save the GPG key on GitHub.

In order to set the key as signing key, run
```bash
git config --global user.signingkey KEY_ID
```
The flag `git -S -m "add: my signed commit"` is used to sign a key.

**Caution:** The setup seems to work properply only with a Linux system.


#### SSH

SSH stands for Secure SHell.
They are used to authenticate a remote connection to a server.

To generate an SSH key, run

```bash
ssh-keygen -t ed25519 -C "mail-address"
```

As for the GPG-key, one will be asked to provide a passphrase.

One can add SSH keys to GitHub as well and use them to sign commits. Both seem to be more suitable in a Linux system.

### Push

To setup version control on a project using Git, one needs to 
call `git init` after having set up a new repository on GitHub.

A hidden `.git` file will be added to the repository,
which will track the Git history.

With `git status` one can ask for the current status on the branch that is checked out.

By default one will be on the main branch (but it is possible to branch out features and issues to multiple workspaces and merge them later).

The status provides the following states for files:

- 'Untracked': This file is new to the repository.
- 'Modified': This file existed and has been changed.
- 'Deleted': This file has existed and is now deleted.
- 'Renamed': This file has the same content, but the name changed.

Here are some useful basic commands:

Stage a file:
```bash
git add name-of-file
```
Stage all unstaged files
```bash
git add .
```
Unstage a staged file
```bash
git reset name-of-file
```

Afterwards, commit the file by writing
```bash
git commit -m "short message"
```

One can call `git log` to view the history of commits.
To add the newly commited work to the remote repository on GitHub,
type `git push`.

### Contributing to other people's repository

In general, one does not have "write access" to others' repositories.

In order to push commits,
one needs therefore to *fork* the repository before.
After forking a (public) repository,
the 'forker' is the owner of the copy of the repository.

Then, to work remotely on it, clone it (e.g. using SSH).

Looking at the remote, the local clone configures works with `git remote -v`
Note that this will set the fork as the origin
and changes in the original repository will not be updated automatically.

To update the fork, one needs to grab the SSH URL, then run
```bash
git remote add upstream git@github.com:original-repo-username/repo-name.git
```
*Remark.*
One can use anything else but `upstream` in the command,
but this seems to the standard convention.

Now type `git fetch upstream` and `git merge upstream/main` in order to get the last changes from the original project.

### Creating new branches

*Branch* = deviation from the main history

`git branch` allows one to view the existing branches.
The asterisk indicates the branch that is currently checked out.

Creating a new branch works with
`git branch branch_name`.

To change to the new branch `branch_name` one calls
```bash
git checkout branch_name
```
or
```bash
git switch branch_name
```

Creating and switching to the new branch works in one go using
```bash
git checkout -b new_branch
```
or
```bash
git switch -c feature
```

To push the new branch to the origin, type in the command line 
```bash
git push -u origin branch_name
```

### Pull Requests

To finally merge changes, one needs to call a *pull request* (*PR*).

When creating a pull request on GitHub
- `base` is the target for the merge,
- `compare` is the new content that should be merged.

GitHub will show a diff that highlights the differences between the branches.

After the pull request, the changes usually go through a `code review`.

If one receives a pull request, one will see a message `Merge pull request #1`.
There are three strategies to this:
1) `Merge`: Brings all of the commits from the head branch, then creates another commit to process and log the merge.
2) `Squash and Merge`: ALL of the commits from the heads branch will be smashed into a single commit. The new commit will be merged into the base branch without creating a merge commit.
3) `Rebase and Merge`: All commits will be reset to come after the latest commit on the base branch. Then one merges the head without a merge commit.

### Code Review Processes

1) Confirm that **CI** (= **C**ontinuous **I**ntegration) passes (roughly code must meet style guidelines and passes tests).
2) If that passes, they will look at the actual code.
3) Pull down the changes locally.

What maintainers might give as a feedback:
- comment with their request
- official 'request changes' comment -> this blocks the pull request from being merged.

### GitHub Pages
Solid option for deploying
- static websites,
- applications that do not require a back-end server to handle logic (i.e. run entirely client-side).

The webpage will by default be available on `user_name.github.io`
and a repository on `user_name.github.io/repo_name`.

### Remaining in Sync with Remote Counterparts

`git fetch`, `git pull`, `git pull --rebase`

To be continued.
