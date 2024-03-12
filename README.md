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