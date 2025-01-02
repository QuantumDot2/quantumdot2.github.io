---
layout: post
title: First Blog Post!
---

# Some notes on using BASH (Exported from Obsidian)

BASH = Bourne again shell.

## Writing Shell Scripts

BASH Shell scripts need to:

1.  Have their executable bit set via `chmod +x name_of_script`
2.  Their first line should specify the shell used - example: `#!/bin/bash`

## Quote Characters

When using quotation marks in (say) an `echo` command:

- `'` means that what-you-see-is-what-you-get
- `"` means that the quote string will be parsed and things like:

```sh
`command`
```

will be executed and the output replaced. Example:

```sh
echo "Hello `whoami`"
```

will lead to the output of `Hello napper`.

### What is interpreted by `"`

Wheras `'` interprets strings literally, when you use `"` the following three are _interpreted_ first:

1.  \` `command` \`
    1. Replaces what's inside the backticks with the output of command
       1. Example:
          1. `echo "The date is ` \` date \``"
2.  $VAR
    1. Replaces the content of the variable $VAR with its value:
       1. note: $VAR refers to shell variables, and $var refers to variables set by the script
3.  \escape_string
    1. Escapes strings like `*, ?, /,` etc. to prevent things like shell expansion.

> **Variable Names**: Lower-case, with underscores to separate words. Ex: `my_variable_name`
>
> **Constants** and **Environment Variable Names**: All caps, separated with underscores, declared at the top of the file. Ex: `MY_CONSTANT`
>
> From [Stack Overflow](https://unix.stackexchange.com/questions/42847/are-there-naming-conventions-for-variables-in-shell-scripts)

## $0 $1 $\*

When a program is executed, such as

```sh
COMMAND file_name -switch1 -switch2
```

Then `$0` refers to `COMMAND`, `$1` refers to `file_name`, `$*` returns an array of `$1, $2, etc`.

## () ; &

When a command like:

```sh
(date; sleep 4; whoami) & echo 'hello'
```

is issued, then the shell will execute: `date` then sleep for 4 seconds, then execute `whoami`. The ampersand causes the first set of commands in parenthesis to execute in the background, then `echo 'hello'` is executed.

The commands inside of `( command1; command2; ...)` are run inside a subshell.

## Piping data

Can send the standard output of a command to another command by piping!

```sh
date | wc
```

This takes the stdout of `date` and sends it to `wc`.

### Tee

Can `tee` the flow of data to send it to a file, like:

```sh
date | tee date_output.txt | wc
```

### Redirection

Can redirect stdout using `>`. For example:

```sh
date >date_output.txt
```

### Redirection and append

Use `>>` to append stdout. For example:

```sh
date >>date_output.txt
```

keeps adding to the end of the output file!

## Shell special character:`*`

The shell replaces `*` with the name of all files (that don't start with a `.`) in the current directory. The program that receives the `*` doesn't know anything about a `*`, the shell just grabs matching filenames and feeds it to the command.

### Other shell special characters: `[a-c]`, `[abce]`, etc.

These can be used to match ranges, such as `ls [a-c]*` will list all files that start with a, b, or c.
`ls [abce]*` will list files that start with a, b, c, or e - etc.

### Other shell special characters: `?`

A question mark `?` will match a **single** character.

## Variable assignment

Can simple say `var=var_value` to assign to the variable `var` the value `var_value`.

```sh
#!/bin/bash
var1='Andy is amazing!'
echo $var1 '- right??'
echo "$var1 - also doubly so"
```

## Sub-Shells

Commands run in `backticks` or parenthesis, or as part of a pipeline, are run in a separate sub-shell. The parent shell spawns a sub-shell - any variables created in the sub-shell are unavailable in the parent shell. Also, if the parent shell sets any variables after the sub-shell process kicks off, they will be unavailable to the sub-shell.

## Back-ticks & running programs

When running programs and using the output to feed another program, the line-feed characters get converted into word separators. For example, let's say you have a file:

```txt
a@example.com
b@example.com
c@example.com
```

If you `cat` the file, you see the three lines above. But if you feed the output to a command, like `mail` then you get something like this:

```sh
mail `cat mailinglist.txt <file_to_send`
```

expands to:

```sh
mail a@example.com b@example.com c@example.com <file_to_send
```
