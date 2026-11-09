# Core Linux Tasks

Notes for the permissions, process management and text processing tasks. Run on Ubuntu 26.04 LTS on an AWS EC2 instance.

## Permissions: a file only I can read and write, others read-only

```bash
echo 'secret' > myfile && chmod 644 myfile && ls -l myfile
```

```
-rw-r--r-- 1 ubuntu ubuntu 7 Nov  9 19:54 myfile
```

**Breakdown:** `-rw-r--r--` means the owner has read and write (`rw-`), the group has read only (`r--`), and everyone else has read only (`r--`).

**Why 644:** each digit is the sum of read (4), write (2) and execute (1). So 6 = 4 + 2 (read and write) for the owner, and 4 (read) for group and others.

**Related:** for files that should be completely private, like SSH keys, I use stricter modes. `chmod 600` on the Bandit SSH key and `chmod 400` on my EC2 `.pem` key, because SSH refuses to use a key that other users can read.

## Process management: start a background process, find its PID, kill it

```bash
sleep 300 &
ps aux | grep '[s]leep 300'
kill $(pgrep -f 'sleep 300')
```

```
[1] 110606
ubuntu    110606  0.0  0.7  15860  7152 pts/0    SN   19:54   0:00 sleep 300
[1]  + terminated  sleep 300
```

**Explanation:**
- `sleep 300 &` starts a process that waits 300 seconds, and `&` runs it in the background so I get my prompt back. The shell printed the job number `[1]` and the PID `110606`.
- `ps aux | grep '[s]leep 300'` finds the process in the process list and shows its PID in the second column.
- `pgrep -f 'sleep 300'` prints just the PID, and `$(...)` passes it to `kill`, which sends the default termination signal. The shell confirmed with `terminated`.

**Snag:** my first attempt used `echo "PID: $!"` in zsh on my Mac and the terminal hung at a `dquote>` prompt, because zsh treats `!` inside double quotes specially and the quote never closed. Ctrl+C got me out, and I avoided `!` inside double quotes after that.

## Text processing: list users whose shell is /bin/bash

```bash
grep '/bin/bash$' /etc/passwd | cut -d: -f1
```

This printed **nothing**, because no user on this machine uses `/bin/bash`. To find out why, I checked my shell and listed every user whose shell ends in `sh`:

```bash
echo $SHELL
grep 'sh$' /etc/passwd
```

```
/usr/bin/zsh
root:x:0:0:root:/root:/bin/zsh
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/usr/bin/zsh
newuser:x:1001:1001::/home/newuser:/bin/sh
```

On this server root and `ubuntu` use zsh and `newuser` uses `/bin/sh`. The same technique works for any shell:

```bash
grep '/zsh$' /etc/passwd | cut -d: -f1
```

```
root
ubuntu
```

**Explanation:**
- Each line in `/etc/passwd` has seven colon-separated fields (`username:x:UID:GID:comment:home:shell`), and the shell is the last one.
- `grep '/bin/bash$'` keeps only lines that **end** with `/bin/bash`, and the `$` anchors the match to the end of the line.
- `cut -d: -f1` splits each line on `:` (`-d:`) and keeps the first field (`-f1`), which is the username.

**What I learned:** an empty result isn't necessarily a mistake. Here it meant no user matched, and checking `/etc/passwd` showed why. The login shell varies between systems, so it's worth checking before assuming `/bin/bash`.
