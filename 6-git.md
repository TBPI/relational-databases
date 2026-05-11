# Git

## Working with Nano
Typically Linux environments include Vi or Vim as a text editor. But Vim is quite user-unfriendly.

Editors like Emacs or Nano may need to be installed manually, but are easier to understand.
To open a file you enter `nano <filename>`.
Once the file is open you see the version of Nano running and the name of the file your are running as well as its content below.

At the bottom there is a list of available keyboard shortcuts, where `^G` (= Help) stands for `Strg + G`, and `M-` stands for a combination with the `Alt` key.

To edit a file, you press `^O` = `Strg + O` in order for the changes being added to the file.

`^C` = `Strg + C` allows you to cancel and go back to editing the file.

`^X = Strg + X` allows you to exit Nano.

## Version control

Version control system: Git, SVN, Mercurial

These tools allow you to track and manage changes in your project.

Usually, a version control system is merged with a **Version control provider**, e.g. GitHub or Gitlab.

## Purpose of sites like GitHub / GitLab

A version control provider offers cloud storage for version-controlled projects.

Features of  version control
- boosts collaboration
- open source or private
- branching
- issue tracker (assign, claim, etc.)

## Repositories

When creating a new repository on GitHub, you typically have the following options:
- *choose a template:* special kind of repository that you can use as a springboard for new repositories (usually not yet set up);
later on useful, e.g. for issue templates or documentation;
- *select an owner:* you or an organization;
- *choose a name of the repository:* do not use spaces;
- *choose whether repository is public or private;*

A README file will appear on the repositories homepage: good for documentation.

A `.gitignore` file allows to specify files which should be ignored by Git.

To clone the repository, you can run in the terminal:
```
    git clone git@github.com:username/repository-name
```

## Security Requirements

This chapter deals with handling security requirements such as SSH and GPG keys in the context of GitHub.

Both use public-private pair.
You keep a private key on your laptop and share a public key with people who need to verify your authenticity.

### GPG
GPG means Gnu Privacy Guard. GPG keys are used to sign files or commits.

To generate a GPG key, need to run (in Bash)

```
gpg --full-generate-key
```
You will be asked to provide a passphrase.

The gpg key will be stored in a hidden and encrypted directory.

To see the list of your public gpg keys, you run
```
gpg --list-secret-keys --keyid-format=long
```

To get the public key, you use

```
gpg --armor --export "<key id>"
```

As `key id` you can use your e-mail address that you provided when creating the key or the short key id provided after
```
sec   rsa3072/KEY_ID
```

Then you can save the gpg key on GitHub.

In order to set the key as signing key, you run
```
git config --global user.signingkey KEY_ID
```
The flag `git -S -m "add: my signed commit"` lets you sign a key.

**Caution:** The setup seems to work properply only with a Linux system.


### SSH

SSH stands for Secure SHell.
They are used to authenticate a remote connection to a server.

To generate an SSH key, run

```
ssh-keygen -t ed25519 -C "mail-address"
```

As for the gpg-key, you will be asked to provide a passphrase.

You can add ssh keys to Github as well and use them to sign commits. Both seem to be more suitable in a Linux system.

## Push

To setup version control on a project using Git, one needs to 
call `git init` after having set up a new repository on GitHub.

A hidden `.git` file will be added to the repository,
which will track the Git history.

With `git status` you can ask for the current status on the branch your in.

By default you will be on the main branch (but it is possible to branch out features and issues to multiple workspaces and merge them later).

The status will provide you with the following states for files:

- 'Untracked': This file is new to the repository.
- 'Modified': This file existed and has been changed.
- 'Deleted': This file has existed and is now deleted.
- 'Renamed': This file has the same content, but the name changed.

If you want to stage a file, you call
```
git add name-of-file
```
If you want to stage all unstaged files, you can simply call
```
git add .
```
If you want to unstage a staged file, you write the following command
```
git reset name-of-file
```

Afterwards you commit the file by writing
```
git commit -m "short message"
```

You can call `git log` to view the history of commits.
To add your work to the remote repository on GitHub, you need to write `git push`.
