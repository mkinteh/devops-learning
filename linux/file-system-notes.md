# File System Notes

Five commands I found useful while working through the Linux module. All of the output below was run on Ubuntu 26.04 LTS on an AWS EC2 instance.

## 1. `ls -lah`

```bash
ls -lah /var/log | head -5
```

```
total 3.6M
drwxrwxr-x 11 root      syslog          4.0K Nov  8 00:16 .
drwxr-xr-x 13 root      root            4.0K Oct  6 17:33 ..
lrwxrwxrwx  1 root      root              39 Jun  4 05:29 README -> ../../usr/share/doc/systemd/README.logs
-rw-r--r--  1 root      root               0 Nov  1 00:35 alternatives.log
```

**What it does:** lists everything in a directory in long format. `-l` shows permissions, owner, group, size and date, `-a` includes hidden files (names starting with `.`), and `-h` shows sizes in human-readable units like `4.0K` instead of raw bytes. Piping into `head -5` keeps only the first five lines.

## 2. `head -n`

```bash
head -n 5 /etc/services
```

```
# Network services, Internet style
#
# Updated from https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml .
#
# New ports will be added on request if they have been officially assigned
```

**What it does:** prints the first N lines of a file, which is a quick way to peek at a big file without opening all of it. `tail -n` does the same for the end, and `tail -f` follows a log file as new lines arrive.

## 3. `find` with filters

```bash
find . -type f -size 1033c ! -executable
```

**What it does:** searches a directory tree for files matching several conditions at once: `-type f` for regular files, `-size 1033c` for exactly 1033 bytes, and `! -executable` to exclude executables. I used it in Bandit Level 5 to narrow many files down to one (see `bandit-notes.md`). Starting the search at `/` and adding `2>/dev/null` searches the whole system without the "Permission denied" noise (Bandit Level 6).

## 4. `file`

```bash
file data
```

**What it does:** reports what a file really is by looking at its contents, not its extension. In Bandit Level 12 it let me peel off layer after layer of gzip, bzip2 and tar compression, because the file names were misleading.

## 5. `ps aux | grep '[s]leep 300'`

```bash
ps aux | grep '[s]leep 300'
```

```
ubuntu    110606  0.0  0.7  15860  7152 pts/0    SN   19:54   0:00 sleep 300
```

**What it does:** `ps aux` lists every running process, and `grep` filters the list down to the one I want. Writing `[s]leep` instead of `sleep` is a small trick: the pattern still matches "sleep", but the `grep` command's own line (which contains the text `[s]leep`) no longer matches, so `grep` doesn't show up in its own results.
