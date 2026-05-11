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
