# OverTheWire Bandit: Levels 0–20 Notes


# Bandit Level 0

## Level Goal

The goal of this level is for you to log into the game using SSH. The host to which you need to connect is **bandit.labs.overthewire.org**, on port 2220. The username is **bandit0** and the password is **bandit0**. Once logged in, go to the Level 1 page to find out how to beat Level 1.

## Solution

```bash
(base) cleanheartedonly@Mac Linux % ssh -p 2220 bandit0@bandit.labs.overthewire.org
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit0@bandit.labs.overthewire.org's password: 
```

## What I’ve learned

### How to log in with ssh

Use the `ssh` command with the username and host, and pass the port with `-p`:

```
ssh -p PORT USERNAME@HOST
```

For example, with username `alice`, host `203.0.113.10` and port `2222`:

```
ssh -p 2222 alice@203.0.113.10
```

# **Bandit Level 0 → Level 1**

## Level Goal

The password for the next level is stored in a file called **readme**located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

## Solution

```bash
bandit0@bandit:~$ ls
readme
bandit0@bandit:~$ cat readme
Congratulations on your first steps into the bandit game!!
Please make sure you have read the rules at https://overthewire.org/rules/
If you are following a course, workshop, walkthrough or other educational activity,
please inform the instructor about the rules as well and encourage them to
contribute to the OverTheWire community so we can keep these games free!

The password you are looking for is: [redacted]

bandit0@bandit:~$ 
```

## What I’ve learned

`ls` + `cat` let you find and read files in the home directory.

# **Bandit Level 1 → Level 2**

## Level Goal

The password for the next level is stored in a file called **-** located in the home directory

## Solution

```bash
bandit1@bandit:~$ ls
-
bandit1@bandit:~$ cat ./-
[redacted]
```

## What I’ve Learned

### Opening a file named `-`

Many Linux commands treat a lone `-` as “read from standard input” rather than a filename, so `cat -` will just sit there waiting for you to type. To get past this, give the file a path so it no longer looks like `-`.

**The reliable fix: prefix with `./`**

```
cat ./-
```

# **Bandit Level 2 → Level 3**

## Level Goal

The password for the next level is stored in a file called `--spaces in this filename--` located in the home directory

## Solution

```bash
bandit2@bandit:~$ ls
--spaces in this filename--
bandit2@bandit:~$ cat "./--spaces in this filename--"
[redacted]
```

## What I’ve learned

### awkward filenames in Linux

**Rule of thumb:** quote it, and prefix `./`

| Problem | Fix | Example |
| --- | --- | --- |
| File named `-` | Prefix with `./` | `cat ./-` |
| Name starts with `-` or `--` | Prefix with `./` | `cat ./--file` |
| Spaces in name | Quote, or escape with `\` | `cat "my file"` or `cat my\ file` |
| Both | Combine them | `cat "./--spaces in this filename--"` |

# **Bandit Level 3 → Level 4**

## **Level Goal**

The password for the next level is stored in a hidden file in the **inhere** directory.

## Solution

```bash
bandit3@bandit:~$ ls
inhere
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ find .
.
./...Hiding-From-You
bandit3@bandit:~/inhere$ cat ./...Hiding-From-You
[redacted]
```

## What I’ve learned

`ls -a` shows hidden files, because names starting with `.` are hidden by default. `find .` lists everything in the directory regardless of hidden status.

# **Bandit Level 4 → Level 5**

## Level Goal

The password for the next level is stored in the only human-readable file in the **inhere** directory. Tip: if your terminal is messed up, try the “reset” command.

## Solution

```bash
bandit4@bandit:~$ cd inhere
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: Non-ISO extended-ASCII text, with NEL line terminators, with escape sequences
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: Non-ISO extended-ASCII text, with no line terminators
bandit4@bandit:~/inhere$ cat ./-file07
[redacted]

```

## What I’ve learned

### Revision: `file ./*`

**What it does:** `file` checks a file’s *contents* and reports its type. `./*` runs it on every file in the directory.

**Why `./*`:** the files are named `-file00` to `-file09`. A leading `-` looks like an option, so `./` is needed to make them read as paths.

**What I did (Bandit level 4)**

1. `ls` showed ten files: `file00` to `file09`
2. `file ./*` showed:
    - `file00` to `file06`, `file08`, `file09`: `data` (binary, not readable)
    - `file07`: **`ASCII text`** (human-readable)
3. `cat ./-file07` printed the password

**Reading the output**

| Output | Meaning |
| --- | --- |
| `ASCII text` | Readable, safe to `cat` |
| `data` | Binary, not readable |

# **Bandit Level 5 → Level 6**

## Level Goal

The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:

- human-readable
- 1033 bytes in size
- not executable

## Solution

```bash
bandit5@bandit:~$ ls
inhere
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ find . -type f -size 1033c ! -executable
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
[redacted]

```

## What I’ve learned

`find` can combine filters such as type, size and permissions to narrow thousands of files down to one. It’s much faster than browsing directories by hand.

# **Bandit Level 6 → Level 7**

## **Level Goal**

The password for the next level is stored **somewhere on the server**and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size

## Solution

```bash
bandit6@bandit:~$ ls
bandit6@bandit:~$ find
.
./.bash_logout
./.bashrc
./.profile
bandit6@bandit:~$ find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
[redacted]
```

## What I’ve Learned

`find` can search the whole system by owner, group and size, and that `2>/dev/null` cleans up the noise from files you aren’t allowed to read.

# **Bandit Level 7 → Level 8**

## **Level Goal**

The password for the next level is stored in the file **data.txt** next to the word **millionth**

## Solution

```bash
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ grep millionth data.txt
millionth	[redacted]
```

## What I’ve Learned

`grep` takes the pattern first and the file second. My first attempt had them the wrong way round and the terminal just hung, until I used Ctrl+C.

![Level 7: running grep with the arguments the wrong way round](screenshots/bandit-level-07-grep-mistake.png)

# **Bandit Level 8 → Level 9**

## **Level Goal**

The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once

## Solution

```bash
bandit8@bandit:~$ ls
data.txt
bandit8@bandit:~$ sort data.txt | uniq -u
[redacted]
```

## What I’ve Learned

Pipes (`|`) let you chain small commands together. `sort` and `uniq` each do one job, and together they answer a question neither can alone. I tried `uniq -u data.txt` on its own first and it didn’t work.

# **Bandit Level 9 → Level 10**

## **Level Goal**

The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several ‘=’ characters.

## Solution

```bash
bandit9@bandit:~$ strings data.txt | grep "=="
}7;========== the
========== password
========== is
========== [redacted]
```

## What I’ve Learned

A file that looks like garbage in `cat` can still hold readable text. `file` tells you what kind of file you’re dealing with, and `strings` pulls out the readable parts.

when I tried `cat data.txt` initially, and it messed up my terminal:

![Level 9: cat on a binary file garbles the terminal](screenshots/bandit-level-09-cat-binary.png)

# **Bandit Level 10 → Level 11**

## **Level Goal**

The password for the next level is stored in the file **data.txt**, which contains base64 encoded data

## Solution

```bash
bandit10@bandit:~$ base64 -d data.txt
The password is [redacted]
```

## What I’ve Learned

- `base64 -d` decodes base64 text back into its original form.
- Base64 is a reversible encoding, not encryption, and the `base64` command can decode it in one step.

# **Bandit Level 11 → Level 12**

## **Level Goal**

The password for the next level is stored in the file **data.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

## Solution

```bash
bandit11@bandit:~$ ls
data.txt
bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
The password is [redacted]
```

## What I’ve Learned

- `tr` maps one set of characters onto another, and mapping A-Z to N-ZA-M (and the same for lowercase) reverses a 13-place rotation.
- ROT13 is a very weak cipher that anyone can reverse with one command. It’s a good contrast to base64 from the last level: both are encodings, not real security.

# **Bandit Level 12 → Level 13**

## **Level Goal**

The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

## Solution

```bash
bandit12@bandit:~$ ls
data.txt
bandit12@bandit:~$ mytmp=$(mktemp -d)
bandit12@bandit:~$ cp data.txt $mytmp
bandit12@bandit:~$ cd $mytmp
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ pwd
/tmp/tmp.Kw42myrA4c
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ xxd -r data.txt > data
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data
data: gzip compressed data, was "data2.bin", last modified: Sun Oct 25 13:37:03 2026, max compression, from Unix, original size modulo 2^32 585
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ mv data data.gz
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ gzip -d data.gz
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data
data: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ mv data data.bz2
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ bzip2 -d data.bz2
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data
data: gzip compressed data, was "data4.bin", last modified: Sun Oct 25 13:37:03 2026, max compression, from Unix, original size modulo 2^32 20480
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ mv data data.gz
gzip -d data.gz
file data
data: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ tar -xf data
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ ls
data  data.txt  data5.bin
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ tar -xf data5.bin
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ ls
data  data.txt  data5.bin  data6.bin
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ mv data6.bin data6.bz2
bzip2 -d data6.bz2
ls
data  data.txt  data5.bin  data6
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data6
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ tar -xf data6
ls
data  data.txt  data5.bin  data6  data8.bin
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Sun Oct 25 13:37:03 2026, max compression, from Unix, original size modulo 2^32 49
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ mv data8.bin data8.gz
gzip -d data8.gz
file data8
data8: ASCII text
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ cat data8
The password is [redacted]
bandit12@bandit:/tmp/tmp.Kw42myrA4c$ 

```

## What I’ve Learned

A file’s extension doesn’t tell you what’s inside it. I used `file` to identify each layer (gzip, bzip2 or tar) and peeled them off one at a time: gzip → bzip2 → gzip → tar → tar → bzip2 → tar → gzip. I also learned that `xxd -r` turns a hexdump back into binary, that `gzip` and `bzip2` need the right file extension before they’ll decompress, and that `tar -xf` extracts a new file with its own name. Working in a `mktemp -d` directory under `/tmp` kept my home directory clean and gave me somewhere I could write.

# **Bandit Level 13 → Level 14**

## **Level Goal**

The password for the next level is stored in

**/etc/bandit_pass/bandit14 and can only be read by user bandit14**

. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Look at the commands that logged you into previous bandit levels, and find out how to use the key for this level.

If you need help with this level: a hint file can be found in the home directory.

Make sure to read the error messages as they are informative.

## Solution

```bash
bandit13@bandit:~$ ls
HINT  sshkey.private
bandit13@bandit:~$ cat HINT 
If you have trouble with this level, note the following:

1) As for all other levels, this level has a website with information:
   https://overthewire.org/wargames/bandit/bandit14.html
2) No, the level is not broken. To verify, see:
   https://status.overthewire.org/
3) The current version of OverTheWire prevents logging in from one
   level to another via localhost. Log out, and see 1)
4) If you get errors, read the error message on your screen.
   We mean it!
bandit13@bandit:~$ exit
logout
Connection to bandit.labs.overthewire.org closed.
(base) cleanheartedonly@Mac Linux % scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit13@bandit.labs.overthewire.org's password: 
sshkey.private                                                      100% 2602    20.3KB/s   00:00    
(base) cleanheartedonly@Mac Linux % chmod 600 sshkey.private
(base) cleanheartedonly@Mac Linux % ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```

## What I’ve Learned

- Servers can authenticate with SSH keys instead of passwords. I copied the private key to my own machine with `scp`, locked its permissions with `chmod 600`, and logged in with `ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220`.
- The port flag is lowercase `p` for `ssh` but uppercase `P` for `scp`, so it’s easy to mix them up.
- SSH refuses to use a private key that other users can read, which is why `chmod 600` is needed.
- The hint file explained that logging in from one level to another via localhost is blocked, so I had to log out and bring the key to my own computer instead.
- Key-based login is the same mechanism used for AWS EC2 `.pem` keys, so I’ll use this again later in the bootcamp.

# **Bandit Level 14 → Level 15**

## **Level Goal**

The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.

## Solution

```bash
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14 | nc localhost 30000
Correct!
[redacted]
```

## What I’ve Learned

- `nc` (netcat) opens a raw connection to a port and sends or receives text. Piping `cat` into it submits the password automatically.
- Programs talk to each other over network ports, and `localhost` refers to the machine you’re on. `nc` is a simple way to talk to a service by hand. This is the same idea as the ports you’ll expose and map in Docker and Kubernetes later.

# **Bandit Level 15 → Level 16**

## **Level Goal**

The password for the next level can be retrieved by submitting the password of the current level to **port 30001 on localhost** using SSL/TLS encryption.

**Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.**

## Solution

```bash
bandit15@bandit:~$ cat /etc/bandit_pass/bandit15 | openssl s_client -connect localhost:30001 -quiet
Connecting to 127.0.0.1
Can't use SSL_get_servername
depth=0 CN=SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN=SnakeOil
verify return:1
Correct!
[redacted]
```

## What I’ve Learned

- `openssl s_client` is like `nc` but with TLS encryption, so it can talk to services that expect an encrypted connection.
- Encrypted services need a TLS-aware client. `nc` sends plain text, so it fails against TLS ports.

# **Bandit Level 16 → Level 17**

## **Level Goal**

The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

**Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.**

## Solution

```bash
bandit16@bandit:~$ nmap -p 31000-32000 localhost
Starting Nmap 7.98 ( https://nmap.org ) at 2026-11-09 12:56 +0000
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00021s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.07 seconds
bandit16@bandit:~$ cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31046 -quiet
Connecting to 127.0.0.1
4067E7F7FF7F0000:error:0A0000F4:SSL routines:ossl_statem_client_read_transition:unexpected message:../ssl/statem/statem_clnt.c:423:
bandit16@bandit:~$ cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31518 -quiet
Connecting to 127.0.0.1
Can't use SSL_get_servername
depth=0 CN=SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN=SnakeOil
verify return:1
[redacted]
bandit16@bandit:~$ cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31691 -quiet
Connecting to 127.0.0.1
4067E7F7FF7F0000:error:0A0000F4:SSL routines:ossl_statem_client_read_transition:unexpected message:../ssl/statem/statem_clnt.c:423:
bandit16@bandit:~$ cat /etc/bandit_pass/bandit16 | openssl s_client -connect localhost:31790 -quiet
Connecting to 127.0.0.1
Can't use SSL_get_servername
depth=0 CN=SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN=SnakeOil
verify return:1
Correct!
-----BEGIN OPENSSH PRIVATE KEY-----
[redacted]
-----END OPENSSH PRIVATE KEY-----

```

## What I’ve Learned

- I had to find the right service before I could use it. `nmap -p 31000-32000 localhost` scanned the range and showed five open ports.
- I then tested the ports one at a time with `openssl s_client -connect localhost:<port> -quiet` and saw three different behaviours:
    - some ports didn’t speak TLS at all and gave an “unexpected message” SSL error (31046 and 31691)
    - one was a TLS echo server that just sent my password back (31518)
    - one accepted the password and returned a private SSH key (31790)
- Port scanning is a standard first step in finding out what a server exposes, and testing each result is how you separate the real service from the decoys.

# **Bandit Level 17 → Level 18**

## **Level Goal**

There are 2 files in the homedirectory: **passwords.old and passwords.new**. The password for the next level is in**passwords.new** and is the only line that has been changed between**passwords.old and passwords.new**

**NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19**

## Solution

```bash
bandit17@bandit:~$ ls
passwords.new  passwords.old
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< [redacted]
---
> [redacted]
```

## What I’ve Learned

- `diff passwords.old passwords.new` compares the two files. `<` marks the line from the old file and `>` the line from the new one.
- `diff` spots the one line that changed between two files. It’s the same idea behind `git diff`, which you’ll use constantly later.

# **Bandit Level 18 → Level 19**

## **Level Goal**

The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.

## Solution

```bash
(base) cleanheartedonly@Mac Linux % ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

backend: gibson-0
bandit18@bandit.labs.overthewire.org's password: 
[redacted]
```

## What I’ve Learned

- `ssh user@host command` runs a single command remotely and prints the result, without starting an interactive shell. That skips the `.bashrc` logout trap.
- `.bashrc` runs at login, and `ssh` can execute a command remotely. This is how scripts and tools like Ansible and CI/CD pipelines run commands on servers without anyone logging in, so it’s directly useful for the DevOps work coming up.

# **Bandit Level 19 → Level 20**

## **Level Goal**

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

## Solution

```bash
bandit19@bandit:~$ ls
bandit20-do
bandit19@bandit:~$ ls -l
total 16
-rwsr-x--- 1 bandit20 bandit19 14880 Oct 25 13:37 bandit20-do
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
[redacted]
```

## What I’ve Learned

The `ls -l` permissions showed an `s` in the owner’s execute position (`-rwsr-x---`). That’s the **setuid** bit, which makes a program run with the privileges of its owner (bandit20) instead of the user who runs it. I used `./bandit20-do cat /etc/bandit_pass/bandit20` to read a file only bandit20 is allowed to read. This is the same principle behind `sudo` and `passwd`. It also shows why setuid programs are a security risk if they’re badly written, because they give regular users temporary elevated access.

# **Bandit Level 20 → Level 21**

## **Level Goal**

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

**NOTE:** Try connecting to your own network daemon to see if it works as you think

## Solution

```bash
bandit20@bandit:~$ ls
suconnect
bandit20@bandit:~$ cat /etc/bandit_pass/bandit20 | nc -l -p 4444 &
[1] 35
bandit20@bandit:~$ ./suconnect 4444
Read: [redacted]
Password matches, sending next password
[redacted]
[1]+  Done                       cat /etc/bandit_pass/bandit20 | nc -l -p 4444
```

## What I’ve Learned

- One command listens on a port and sends bandit20’s password, and the setuid `suconnect` connects back, verifies it and returns bandit21’s password. `&` lets both run from one terminal.
- How to act as a network server with `nc -l`, how clients and servers connect, and how to run things in the background with `&`. That’s the same client/server idea behind web servers and the Docker and Kubernetes services you’ll be building later.