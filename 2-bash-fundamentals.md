# Bash fundamentals

## Understanding the command line

*Command line* = text input interface where the user can write the command and submit/execute them (usually by hitting enter).

*Terminal* = special application which provides the interface for command lines for system-level commands.
Terminal emulators are 'enhanced' terminals which provide additional functionality to the usual terminal.

<sub>
    Microsoft has two standard terminals the Powershell and Command Prompt.

</sub>
 

*Shell* = software that wraps the command line and interprets the input and returns the outputs.

## Speed up productivity in the command line

Similar commands for MacOS and Linux as they both have there roots in Unix.
For Microsoft they are different.

*Up arrow key* = cycle backwards in history.

*Down arrow key* = cycle downwards in history.

*tab* = in many shells for autofill

*Control + C* = kill process

*Control + Z* = multitask, i.e. current process in background while your are returned to command line

*!!* = run command twice

*Control + L* = new terminal.

## What is Bash?

**BASH = Bourne Again SHell.**

`pwd` = prints current working directory to shell.

`cd` = change directories.
You can move to a certain directory by giving the full path starting with `/` or move relatively. `..` allows you to move upwards.

`ls` = lists contents of the directory. `-a` flag shows hidden files, `-l` flag shows file permissions. You can use `less` or `cat` to view contents of a file

`mkdir` = creates new directory.

`touch` = creates new file.

<sub>
In the powershell the creation of a new file is relatively complicated, you need to call `New-Item filename.md -ItemType File`
</sub>

`mv` = allows you to move or rename a file

`rm` = allows you to delete a file. With `-r` flag it deletes a directory. With `-f` flag you can delete protected files.

`cp` = copy a file to a new directory.

`echo` = allows you to print to the terminal. You can write directly into the file using `echo "Text" > filename`.

`head`

`tail`

`chown`

`chmod`

`man` = manual.

### Command options and flags

`--version` flag = prints current version
`--help` flag = prints instructions

There are long (usually two hyphens) and short forms (one hyphen).
Short forms allow you to easily chain different flags together. 
