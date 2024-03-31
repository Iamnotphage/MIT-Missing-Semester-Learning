# The-Missing-Semester-Learning

Some notes about this tiny course.

* [Offical Website](https://missing.csail.mit.edu/)
* [Record](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J)

***
# Course overview + the shell

Here I installed *WSL* in my PC.

**Exercises:**

*1. Test the Shell*

```shell
chen@Iamnotphage:~/missing-semester$ echo $SHELL
/bin/bash
```

*2. Create a new directory*

```shell
chen@Iamnotphage:~$ cd /tmp
chen@Iamnotphage:/tmp$ mkdir missing
chen@Iamnotphage:/tmp$ ls
missing
```

*3. The `man` program is your friend.*

```shell
chen@Iamnotphage:/$ man touch
```

*4. Use `touch` to create a new file called `semester` in `missing`*
```shell
chen@Iamnotphage:/tmp/missing$ touch semester
chen@Iamnotphage:/tmp/missing$ ls
semester
```

*5. Write the following into that file, one line at a time:*

```shell
chen@Iamnotphage:/tmp/missing$ echo \#'!'/bin/sh > semester
chen@Iamnotphage:/tmp/missing$ echo curl --head --silent https://missing.csail.mit.edu >> semester
```

'\' : escape character

Be careful with `>` and `>>`.

*6. Try to execute the file*
```shell
chen@Iamnotphage:/tmp/missing$ ./semester
-bash: ./semester: Permission denied
chen@Iamnotphage:/tmp/missing$ ls -l
total 4
-rw-r--r-- 1 chen chen 61 Mar 12 16:53 semester
```

Cuz we don't have permission to `execute`.

*7. Run the command by explicitly starting the `sh` interpreter, and giving it the file `semester` as the first argument.*

```shell
chen@Iamnotphage:/tmp/missing$ sh semester
HTTP/2 200
server: GitHub.com
content-type: text/html; charset=utf-8
last-modified: Sat, 02 Mar 2024 14:52:48 GMT
access-control-allow-origin: *
etag: "65e33d40-2015"
expires: Tue, 05 Mar 2024 20:43:31 GMT
cache-control: max-age=600
x-proxy-cache: MISS
x-github-request-id: 6D24:D0C6A:76EDD:7E1ED:65E7819A
accept-ranges: bytes
date: Tue, 12 Mar 2024 08:58:41 GMT
via: 1.1 varnish
age: 0
x-served-by: cache-nrt-rjtf7700051-NRT
x-cache: HIT
x-cache-hits: 1
x-timer: S1710233921.208166,VS0,VE220
vary: Accept-Encoding
x-fastly-request-id: 0e9359738aa709570d60063609966a97ec2d50e0
content-length: 8213
```

Why `./`  failed and `sh` success? `sh` can execute the script without the `x` permission.

*8. Look up the `chmod` program.*

```shell
chen@Iamnotphage:/tmp/missing$ man chmod
```

*9. Use `chmod` to make it possible to run the command `./semester` rather than having to type `sh semester`.*

```shell
chen@Iamnotphage:/tmp/missing$ chmod +x semester
chen@Iamnotphage:/tmp/missing$ ls -l
total 4
-rwxr-xr-x 1 chen chen 61 Mar 12 16:53 semester
```

[Reason: SheBang(HashBang)](https://zh.wikipedia.org/wiki/Shebang)

*10. Use `|` and `>` to write the “last modified” date output by `semester` into a file called `last-modified.txt` in your home directory.*

```shell
chen@Iamnotphage:/home$ sudo touch last-modified.txt
chen@Iamnotphage:/home$ sudo chmod 777 last-modified.txt
chen@Iamnotphage:/home$ ls -l
total 4
drwxr-x--- 4 chen chen 4096 Mar 12 17:51 chen
-rwxrwxrwx 1 root root    0 Mar 12 17:44 last-modified.txt
chen@Iamnotphage:/home$ cd -
/tmp/missing
chen@Iamnotphage:/tmp/missing$ sudo ./semester | grep "last-modified" > /home/last-modified.txt
```

Seems like the file in `/home` needs `sudo`, otherwise it will turn out to be permission problem.

*11. Write a command that reads out your laptop battery’s power level or your desktop machine’s CPU temperature from `/sys`.*

```shell
chen@Iamnotphage:~$ cat /sys/class/power_supply/BAT1/capacity
98
```

***

# Shell Tools and Scripting

**Exercises:**

*1. Read [`man ls`](https://www.man7.org/linux/man-pages/man1/ls.1.html) and write an `ls` command that lists files in the following manner*

- Includes all files, including hidden files
- Sizes are listed in human readable format (e.g. 454M instead of 454279954)
- Files are ordered by recency
- Output is colorized 

```shell
chen@Iamnotphage:~/missing-semester$ ls -alth --color=auto
total 24K
drwxr-x--- 4 chen chen 4.0K Mar 13 12:46 ..
drwxr-xr-x 4 chen chen 4.0K Mar 13 10:41 .
drwxr-xr-x 2 chen chen 4.0K Mar 13 10:28 bar
drwxr-xr-x 2 chen chen 4.0K Mar 13 10:28 foo
-rwxr-xr-x 1 chen chen  292 Mar 13 10:25 forloop.sh
-rwxr-xr-x 1 chen chen   81 Mar 12 20:49 helloworld.sh
```

*2. Write bash functions `marco` and `polo` that do the following. Whenever you execute `marco` the current working directory should be saved in some manner, then when you execute `polo`, no matter what directory you are in, `polo` should `cd` you back to the directory where you executed `marco`. For ease of debugging you can write the code in a file `marco.sh` and (re)load the definitions to your shell by executing `source marco.sh`.*

```bash
# marco.sh
marco(){
        export MARCO=$(pwd)
}

polo(){
        cd "$MARCO"
}
```


```shell
chen@Iamnotphage:/$ source marco.sh
chen@Iamnotphage:/$ marco
chen@Iamnotphage:/$ echo "$MARCO"
/
chen@Iamnotphage:/$ cd ~
chen@Iamnotphage:~$ polo
chen@Iamnotphage:/$ pwd
/
```

*3. Say you have a command that fails rarely. In order to debug it you need to capture its output but it can be time consuming to get a failure run. Write a bash script that runs the following script until it fails and captures its standard output and error streams to files and prints everything at the end. Bonus points if you can also report how many runs it took for the script to fail.*

in `err.sh`:

```shell
#!/usr/bin/env bash

n=$(( RANDOM % 100 ))

if [[ n -eq 42 ]]; then
        echo "Something went wrong"
        >&2 echo "The error was using magic numbers"
        exit 1
fi

echo "Everything went according to plan"
```

in `script.sh`:

```shell
#!/bin/bash

./err.sh > ./out.log 2> ./out.log

count=1

while [[ $? -eq 0 ]]
do
        let count++
        ./err.sh >> ./out.log 2>> ./out.log
done

cat ./out.log
echo "run times: $count"
```

Now back to the terminal.

```shell
chen@Iamnotphage:~/missing-semester$ bash script.sh err.sh
Everything went according to plan
Everything went according to plan
.................................
Everything went according to plan
Something went wrong
The error was using magic numbers
run times: 72
```

`sh` or `bash` depends on the **SheBang**. `sh` and `bash` are kinda different.

*4. Write a command that recursively finds all HTML files in the folder and makes a zip with them. Note that your command should work even if the files have spaces (hint: check `-d` flag for `xargs`).*

`find` and `xargs` is required. We can check the manual by typeing : `man xargs`

For a better test of the command, I `touch` some `html` files.

```shell
chen@Iamnotphage:~/missing-semester$ mkdir TestXargs
chen@Iamnotphage:~/missing-semester$ cd TestXargs/
chen@Iamnotphage:~/missing-semester/TestXargs$ touch {a,b,c,d,e,f}.html
chen@Iamnotphage:~/missing-semester/TestXargs$ mkdir g
chen@Iamnotphage:~/missing-semester/TestXargs$ cd g
chen@Iamnotphage:~/missing-semester/TestXargs/g$ touch {h,i,j,k,l}.html
```

And execute this:

```shell
chen@Iamnotphage:~/missing-semester$ find . -type f -name "*.html" | xargs -d "\n" tar -cvzf myzip.tar.gz
```

`-d` means : delimiter.

As we can see that when we use `find` command, it will output the file and seperate by `\n`.

Finally we just use `tar`.

*5. (Advanced) Write a command or script to recursively find the most recently modified file in a directory. More generally, can you list all files by recency?*

I list the top 8 files that most recently modified. (sort of confused about this exercise)

```shell
chen@Iamnotphage:~/missing-semester$ ls -ltR | head -n 10
.:
total 40
-rw-r--r-- 1 chen chen  227 Mar 13 20:42 myzip.tar.gz
drwxr-xr-x 3 chen chen 4096 Mar 13 20:41 TestXargs
-rw-r--r-- 1 chen chen 1279 Mar 13 16:58 out.log
-rwxrwxrwx 1 chen chen  176 Mar 13 16:52 script.sh
-rwxr-xr-x 1 chen chen  202 Mar 13 16:50 err.sh
-rwxr-xr-x 1 chen chen  292 Mar 13 16:47 forloop.sh
-rw-r--r-- 1 chen chen   57 Mar 13 12:57 marco.sh
drwxr-xr-x 2 chen chen 4096 Mar 13 10:28 bar
```

# Editors (Vim)

Well, actually every single CS student can use this tool.

Vim has multiple operating modes:

| Modes | Meaning |
|-------|---------|
|**Normal**|for moving around a file and making edits|
|**Insert**|for inserting text|
|**Replace**|for replacing text|
|**Visual**|for selecting blocks of text|
|**Command-line**|for running a command|

Some **movement** commands;

- Basic movement: `hjkl` (left, down, up, right)
- Words: `w` (next word), `b` (beginning of word), `e` (end of word)
- Lines: `0` (beginning of line), `^` (first non-blank character), `$` (end of line)
- Screen: `H` (top of screen), `M` (middle of screen), `L` (bottom of screen)
- Scroll: `Ctrl-u` (up), `Ctrl-d` (down)
- File: `gg` (beginning of file), `G` (end of file)
- Line numbers: `:{number}<CR>` or `{number}G` (line {number})
- Misc: `%` (corresponding item)
- Find: `f{character}`, `t{character}`, `F{character}`, `T{character}`
    - find/to forward/backward {character} on the current line
    - `,` / `;` for navigating matches
- Search: `/{regex}`, `n` / `N` for navigating matches

**Selection**

Visual modes:

- Visual: `v`
- Visual Line: `V`
- Visual Block: `Ctrl-v`

Can use movement keys to make selection.

**Edits**

- `i` enter Insert mode
    - but for manipulating/deleting text, want to use something more than backspace
- `o` / `O` insert line below / above
- `d{motion}` delete {motion}
    - e.g. `dw` is delete word, `d$` is delete to end of line, `d0` is delete to beginning of line
- `c{motion}` change {motion}
    - e.g. `cw` is change word
    - like `d{motion}` followed by `i`
- `x` delete character (equal do `dl`)
- `s` substitute character (equal to `cl`)
- Visual mode + manipulation
    - select text, `d` to delete it or `c` to change it
- `u` to undo, `<C-r>` to redo
- `y` to copy / “yank” (some other commands like `d` also copy)
- `p` to paste
- Lots more to learn: e.g. `~` flips the case of a character

**Counts**

- `3w` move 3 words forward
- `5j` move 5 lines down
- `7dw` delete 7 words

**Modifiers**

You can use modifiers to change the meaning of a noun. Some modifiers are `i`, which means “inner” or “inside”, and `a`, which means “around”.

- `ci(` change the contents inside the current pair of parentheses
- `ci[` change the contents inside the current pair of square brackets
- `da'` delete a single-quoted string, including the surrounding single quotes

**Exercises:**

*1. Complete `vimtutor`. Note: it looks best in a [80x24](https://en.wikipedia.org/wiki/VT100) (80 columns by 24 lines) terminal window.*

```shell
chen@Iamnotphage:~/missing-semester$ vimtutor
```

And you just follow the tutor. It's so funny.

*2. Download our [basic vimrc](https://missing.csail.mit.edu/2020/files/vimrc) and save it to `~/.vimrc`. Read through the well-commented file (using Vim!), and observe how Vim looks and behaves slightly differently with the new config.*

Just read.

*3. Install and configure a plugin: [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim).
	3.1. Create the plugins directory with `mkdir -p ~/.vim/pack/vendor/start`
    3.2. Download the plugin: `cd ~/.vim/pack/vendor/start; git clone https://github.com/ctrlpvim/ctrlp.vim`
    3.3. Read the [documentation](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md) for the plugin. Try using CtrlP to locate a file by navigating to a project directory, opening Vim, and using the Vim command-line to start `:CtrlP`.
    3.4. Customize CtrlP by adding [configuration](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md#basic-options) to your `~/.vimrc` to open CtrlP by pressing Ctrl-P.*

Ctrl-P is a vim tool, [more](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md) about it.

I found that I donot need to customize to open CtrlP by pressing Ctrl-P. Maybe it's the version problem.

*4. To practice using Vim, re-do the [Demo](https://missing.csail.mit.edu/2020/editors/#demo) from lecture on your own machine.*

Yes I did.

*5. Use Vim for _all_ your text editing for the next month. Whenever something seems inefficient, or when you think “there must be a better way”, try Googling it, there probably is. If you get stuck, come to office hours or send us an email.*

Kinda difficult.

*6. Configure your other tools to use Vim bindings (see instructions above).*

```shell
chen@Iamnotphage:~$ vim vimrc
```

*7. Further customize your `~/.vimrc` and install more plugins.*
