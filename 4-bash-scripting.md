# Bash scripting

You can actually write small files that can perform more complex automated tasks in the terminal.
This refers to *Bash scripting*.

**Caution!** Windows does not know what a `.sh` script is. Therefore, we need to call them in a virtual machine. <sub>On my computer, I can call `bash` to run Linux in the terminal.</sub>

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
Afterwards, you can simply run the file by typing `./file_name.sh`.
Note that you can also put arguments after this command which can be passed into the file.

You can see bash and other built-in commands using `ls /`.

Bash has variables.

**CAUTION!** There cannot be spaces between the equal sign when initializing a variable!

For using a variable you place `$` in front of the variable name.
If you want to use the last used variable, you can simply write `$?`.

In order to write several commands in one line, use a semi-colon.

Moreover, the user (in the command line) can initialize a variable. To get this input, you need to put `read` in front of the variable.

Comments in a bash file work as in Python by starting the line with `#`.
In order to comment out multiple lines, 
you need to type in
```
: '
things to comment out
'
```

### Passing arguments into the file
When executing a file, 
you can pass arguments afterwards, e.g. by
```
./file_name.sh arg1 arg2 arg3
```
In this example, you have passed three arguments.

In order to access an argument given into the document, you can use `$number_of_argument`.

### If statements
The semantics of an if-statement is
```
if [[ CONDITIONS ]]
then
    do something
elif [[ CONDITIONS ]]
then
    do something else
else
    do again something else
fi
```
For integers there are several possibilities to for the conditions:
- `[[ $1 -eq <number> ]]`: first argument must be equal to `number`,
- `[[ $1 -lt <number> ]]`: first argument must be less than `number`,
- `[[ $1 -le <number> ]]`: first argument must be less or equal than `number`,
- `[[ $1 -gt <number> ]]`: first argument must be greater than `number`,
- `[[ $1 -ge <number> ]]`: first argument must be greater than `number`.

*Remark.*
We can find out more about the conditions using `help [[ expression ]]`.

To test several expressions, you can use:
- `EXPR1 && EXPR2` testing whether both expressions are true,
- `EXPR1 || EXPR2` testing whether `EXPR1` or `EXPR2` are true.

### Loops

Here is how a `for` loop works in bash
```
for (( i = ))

```
