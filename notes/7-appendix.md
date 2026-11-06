# Appendix

## Code Editors

### What is a code editor?

**Code editor =** any application that allows one to edit code.

**Integrated Development Environment (IDE) =** full application that allows one to compile, run, and debug code while editing it.
For example: Visual Studio (by Microsoft), Xcode (by Apple), Android Studio (by Google).

A **cloud-based editor** is an online tool that allows one to write, edit, and manage code or text directly through a web browser without needing to install software locally.
For example, Replit, GitHub Codespaces, Ona (formerly Gitpod).

### How to create a project and run the code locally in VS Code?

VS Code considers projects to be `workspaces`.

Some basic bash commands make it easy to work with the terminal.

## Understanding the command line

*Command line* = text input interface where the user can write the command and submit/execute them (usually by hitting enter).

*Terminal* = special application which provides the interface for command lines for system-level commands.
Terminal emulators are 'enhanced' terminals which provide additional functionality to the usual terminal.

*Remark.*
Microsoft has two standard terminals the Powershell and Command Prompt.
 

*Shell* = software that wraps the command line and interprets the input and returns the outputs.

### Speed up productivity in the command line

Similar commands for MacOS and Linux as they both have there roots in Unix.
For Microsoft they are different.

`Up arrow key` = cycle backwards in history.

`Down arrow key` = cycle downwards in history.

`tab` = in many shells for autofill

`Ctrl + C` = kill process

`Control + Z` = multitask, i.e. current process in background while your are returned to command line

`!!` = run command twice

`Control + L` = new terminal.

### What is Bash?

**BASH = *B*ourne *A*gain *Sh*ell.

Here are some basic commands:

`pwd` = prints current working directory to shell.

`cd` = change directories.
On can move to a certain directory by giving the full path starting with `/` or move relatively. `..` allows you to move upwards.

`ls` = lists contents of the directory.
`-a` flag shows hidden files,
`-l` flag shows file permissions.

`less` = read content of the file in terminal

`cat` = read content of the file in terminal

`mkdir` = creates new directory.

`touch` = creates new file.

`mv` = move or rename a file

`rm` = delete a file.
With `-r` flag it deletes a directory.
Use `-f` to delete protected files.

`cp` = copy a file to a new directory.

`echo` = print to the terminal.
For writing directly into the file using `echo "Text" > filename`.

`head` = prints the first lines of a file (default 10).
Use `-n 5` to show only the first 5 lines.

`tail` = prints the last lines of a file (default 10).
Use `-f` to follow a file live as new lines are added, e.g. log files.

`chown` = change the owner (and group) of a file or directory, e.g. `chown user:group filename`.
With `-R` flag it applies to all contents of a directory.

`chmod` = change the permissions (read `r`, write `w`, execute `x`) of a file, e.g. `chmod +x script.sh` makes a script executable.

`man` = shows the manual page of a command, e.g. `man ls`.
Quit with `q`.

#### Command options and flags

`--version` flag = prints current version
`--help` flag = prints instructions

There are long (usually two hyphens) and short forms (one hyphen).
Short forms allow you to easily chain different flags together.

#### Difference between PowerShell and Bash
Windows PowerShell has aliases such as `ls`, `cd`, `pwd`, `rm`, `mkdir`,
but they behave differently and other Bash commands are missing.
VS Code makes it easy to use Bash
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

Creating a directory `new_directory` in Bash:
```bash
mkdir new_directory
```
The same command works in Windows PowerShell as well,
but one can also use:
```powershell
New-Item -ItemType Directory -Name "new_directory"
```

### Live Server

For a webpage,
one often starts with a file named `index.html`.
The easiest way to open the file afterwards (in live version)
is to use the *Live Server* extension.
Then click on *Go Live* or load `http://localhost:5500/` in the browser,
to see the live changes.

### Useful keyboard shortcuts for maximizing productivity in VS Code

`Shift + Alt + F` = runs the configured formatter.

`Ctrl + Shift + F` = search the text contents of all the files in the current workspace.

`Ctrl + Shift + H` = find and replace.

`Ctrl + Shift + K` = delete the current line.

`Ctrl + B` = hide the sidebar.

`Ctrl + Alt + Up/Down` = extend the cursor to consecutive lines above/below.

`Ctrl + Shift + P` = opens command palette.

### Nano
Typically Linux environments include Vi or Vim as a text editor.
But Vim is quite user-unfriendly.

Editors like Emacs or Nano may need to be installed manually,
but are easier to understand.
*Nano* is often preinstalled, e.g. on Ubuntu, Debian.

To open a file, enter `nano <filename>`.
Once the file is open,
one sees the version of Nano running
and the name of the file to be edited as well as its content below.

At the bottom there is a list of available keyboard shortcuts, where `^G` (= Help) stands for `Ctrl + G`, and `M-` (*Meta key*) stands for a combination with the `Alt` key (or `ESC`).

After editing a file,
press `^O` = `Ctrl + O` in order for the changes being saved in the file.

`^C` = `Ctrl + C` allows one to cancel and go back to editing the file (inside the prompt).

`^X = Ctrl + X` allows one to exit Nano.
Nano will ask to save unsaved changes.

## Git

### Version control

Version control system: Git, SVN, Mercurial.

These tools make it possible to track
and manage changes in the project.

Usually, a version control system is used together with a **version control provider**,
e.g. GitHub or GitLab.
The version control system can be hosted on the version control provider (in a cloud).
The project is available on the version control provider for all co-workers,
and all co-worker can track the changes made.

### Purpose of sites like GitHub / GitLab

A version control provider offers cloud storage for version-controlled projects.

Features of version control providers:
- boosts collaboration,
- public or private repositories,
- branching,
- issue tracker (assign, claim, etc.).

### Repositories

When creating a new repository on GitHub,
there are typically the following options:
- *choose a template:* special kind of repository that one can use as a springboard;
    later on useful, e.g. for issue templates or documentation;
- *select an owner:* the private account or an organization;
- *choose a name of the repository:* do not use spaces;
- *add optional files:* `README.md`, `.gitignore` and licenses.
- *choose whether repository is public or private*.

A `README.md` (if ticked yes) file will appear on the repository's homepage:
good for documentation.

A `.gitignore` file allows one to specify files which should be ignored / not tracked by Git.

To clone the repository, run in the terminal:
```bash
git clone git@github.com:username/repository-name.git
```

### Security Requirements

This Section deals with handling security requirements such as SSH and GPG keys in the context of GitHub.

Both use a public-private key pair.
Keep the private key on the personal laptop
and share a public key with people who need to verify the authenticity.

#### GPG
GPG means GNU Privacy Guard. GPG keys are used to sign files or commits.

To generate a GPG key, run

```bash
gpg --full-generate-key
```
One will be asked to provide a passphrase.

The GPG key will be stored in the hidden directory `-/.gnupg`.
The private key is protected by the passphrase.

To see the list of the public GPG keys and the key ID, run
```bash
gpg --list-keys --keyid-format=long
```
To list the private GPG keys, run
```bash
gpg --list-secret-keys --keyid-format=long
```
To get the public key, use
```bash
gpg --armor --export "<key id>"
```

As `key id` one can use the e-mail address
that is provided when creating the key or the short key id provided after
```bash
sec   rsa3072/KEY_ID
```

Then save the GPG key on GitHub:
```text
GitHub > Settings > SSH and GPG keys > New GPG key
```
Paste the whole output,
including the `BEGIN` and `END` lines.

In order to set the key as signing key, run
```bash
git config --global user.signingkey KEY_ID
```
The flag `git commit -S -m "add: my signed commit"` is used to sign a commit.

#### SSH

SSH stands for Secure Shell.
SSH keys are used to authenticate a remote connection to a server.

To generate an SSH key, run

```bash
ssh-keygen -t ed25519 -C "mail@example.com"
```

As for the GPG-key, one will be asked to provide a passphrase.

One can add SSH keys to GitHub as well and use them to sign commits.

### Basic Git workflow

To setup version control on a project using Git,
start with 
```bash
git init
```
This creates a new local repository.
To connect with the version control provider,
and make `git push` work,
type in the terminal
```bash
git remote add origin <url>
```

If the repository already exists on GitHub,
run instead
```bash
git clone https://github.com/username/repository-name.git custom-folder
```
The `custom-folder` is optional.
Most of the time, one clones the full repository.

A hidden `.git` folder will be added to the repository,
which will track the Git history.

With `git status` one can ask for the current status on the branch that is checked out.

By default one will be on the main branch (but it is possible to branch out features and issues to multiple workspaces and merge them later).

The status provides the following states for files:

- 'New file': This is a staged new file (not tracked up to now).
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
git restore --staged name-of-file
```

Afterwards, commit the staged changes by writing
```bash
git commit -m "short message"
```

One can call `git log` to view the history of commits.
To add the newly committed work to the remote repository on GitHub,
type `git push`.
The first push of a branch usually needs
```bash
git push -u origin main
```

### Contributing to other people's repository

In general, one does not have "write access" to others' repositories.

In order to push commits,
one needs therefore to *fork* the repository before.
After forking a (public) repository,
the 'forker' is the owner of the copy of the repository.

Then, to work locally on it, clone it (e.g. using SSH).

To update the fork, one needs to grab the SSH URL, then run
```bash
git remote add upstream git@github.com:original-repo-username/repo-name.git
```
*Remark.*
One can use anything else but `upstream` in the command,
but this seems to be the standard convention.

Now type `git fetch upstream` and `git merge upstream/main` in order to get the latest changes from the original project.

### Creating new branches

*Branch* = independent line of development.

`git branch` allows one to view the existing branches.
The asterisk indicates the branch that is currently checked out.

Creating a new branch works with

```bash
git branch branch_name
```

To change to the new branch `branch_name`, one calls
```bash
git checkout branch_name
```
or
```bash
git switch branch_name
```

Creating and switching to the new branch works in one go using
```bash
git checkout -b branch_name
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

To finally merge changes, one needs to create a *pull request* (*PR*).
PRs are a GitHub feature.

It is possible to merge branches locally without a pull request using `merge`.
After the implementation of a new feature on a side branch `branch_name`,
one can merge the new feature to the main branch as follows:
```bash
git switch main
git merge branch_name
```
It is best practice to check the compatibility of the new feature before merging.
Therefore, doing a pull request and a code review is the better way of merging branches.

When creating a pull request on GitHub
- `base` is the target for the merge,
- `compare` is the new content that should be merged. It is the head branch.

GitHub will show a diff that highlights the differences between the branches.

After the pull request, the changes usually go through a *code review*.
The next steps after PR depend on the branch protection rules.
When collaborating, it is best practice to define those rules before.

If one receives a pull request on GitHub,
one has to approve it and the merge commit will
look something like this: `Merge pull request #1` (for the `Merge` method below).

There are three merge methods:
1) `Merge`: Brings all of the commits from the head branch, then creates a merge commit that joins the two histories.
2) `Squash and Merge`: ALL of the commits from the head branch will be smashed into a single commit. The new commit will be merged into the base branch without creating a merge commit.
3) `Rebase and Merge`: All commits will replayed (i.e. re-applied with new hashes) on the base branch. The merge itself happens without a commit.

### Code Review Processes

1) Confirm that **CI** (= **C**ontinuous **I**ntegration) passes (roughly code must meet style guidelines and passes tests).
2) If that passes, the reviewer will look at the actual code.
3) Pull down the changes locally.

What maintainers might give as feedback:
- comment with their request
- official *request changes* comment -> usually this blocks the pull request from being merged (refer to the GitHub Rulesets).

### GitHub Pages
Solid option for deploying:
- static websites,
- applications that do not require a back-end server to handle logic (i.e. run entirely client-side).

One can do a personal webpage on GitHub as follows:
- Create a repository with repository name `user_name.github.io`.
- Then the user site is `https://user_name.github.io`.

Any other repository can be seen on `https://user_name.github.io/repo_name`.
To do so, one has to choose a branch or GitHub Actions as a sources in `Settings > Pages`.

### Remaining in Sync with Remote Counterparts

`git fetch` = download remote changes without changing local branches,

`git pull` - `git fetch` merge into current branch,

`git pull --rebase` - `git fetch` merge into current branch and replay local commits on top.

*To be continued.*
