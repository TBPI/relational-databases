# Bash scripting

You can actually write small files that can perform more complex automated tasks in the terminal.
This refers to *Bash scripting*.

**Caution!** Windows does not know what a `.sh` script is. Therefore, we need to call them in a virtual machine.

The first line of a bash script start usually with
`#!` the *shebang*.
It tells the system which interpreter should be used to run the script, e.g.
`usr/bin/bash`, `bin/bash`, or `bin/sh`.

We give a list of bash commands and programs in the following.

## Writing a file
A file that is executed by the `sh`- or `bash`-interpreter is has usually the ending `.sh`.
Here `sh` stands for *shell* and `bash` for *bourne-again shell*.

In order to execute your file, it is good to find out where the `bash` interpreter is located. This can be done by `which bash` in the command line.

Then add to your file the *shebang* `#!<path_to_bash_interpreter>`.

Bash has variables. There cannot be spaces between the equal sign when initializing a variable!

For using a variable you place `$` in front of the variable name.
Say you have created
