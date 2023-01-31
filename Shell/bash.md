---
title: Bash
description: 
published: true
date: 2023-01-31T16:52:57.224Z
tags: 
editor: markdown
dateCreated: 2023-01-31T15:30:17.875Z
---

### shopt and set
`shopt` is a substitute for `set`(which is inherited from other Bourne-style shells like `ksh`).

```bash
shopt -p               #view option on/off settings,similiar with *set -o*
shopt –o –s noclobber  #set -o noclobber
shopt -o -u physical   #set +o physical
```

### extglob
In addition to the traditional [glob](http://en.wikipedia.org/wiki/Glob_(programming\)) (supported by all _Bourne-style shells_), Bash (and Korn Shell) offers extended globs, which have the expressive power of regex. Korn shell enables these by default.

```bash
shopt -s extglob #set
shopt -u extglob #unset
```
```bash
#pattern-list is separated with '|'
?(pattern-list)         <= 1
*(pattern-list)         >= 0
+(pattern-list)         >= 1
@(pattern-list)         or
!(pattern-list)         !=

rm -rf !(*.ll)
rm -rf @(*.s|*.ll)          
```

References:
- <http://wiki.bash-hackers.org/internals/shell_options>

### test
`test` and `[`(acompany with `]`) are tools that does test work.
It returns 0(true) or 1(false)! Just ignore the oddity and take a look at the
examples below.

```bash
test 3 -gt 4 && echo True || echo false   #false
[ "abc" != "def" ];echo $?                #0
test -d "$HOME" ;echo $?                  #0
[ ! -f /etc/hosts ] && echo "Found" || echo "Not found"  #typical usage, or *test ! -f /etc/hosts*
```

### pattern match

Use `man bash` for help, `parameter` is the whole string(s) to be manipulate, delete those matched.

```bash
${parameter#word}      #matches beginning
${parameter##word}     #matches beginning
${parameter%word}      #matches tail
${parameter%%word}     #matches tail
```
```bash
FILE="example.tar.gz"
echo "${FILE%%.*}"     #example
echo "${FILE%.*}"      #example.tar
echo "${FILE#*.}"      #tar.gz
echo "${FILE##*.}"  #gz
```

- change prefix of several file names: 

```bash
$ ls
old old.c old.tar.gz
$ for file in old*;do mv $file new${file#old};done;ls
new new.c new.tar.gz
```

### interactive substitution

```bash
$N                 # the Nth argument of previous command line (N=0,1,...)
$M-N               # the arguments list from M to N (both inclusive)
$-N                # DONT use it except !! (same as "!-1")
# event designator !! can be abbreviated to ! when using a word designator
!string            # DONT use
!?string?          # DONT use
!:^                # 1st argument, "!:1"
!:$                # last argument
!:*                # all argument except 1st
!:h                # dirname
!:t                # basename
!:r                # all but extension
!:e                # file extension
```                 


### output to console+files
```bash
{{cmd} > >(tee stdout.log);} 2> >(tee stderr.log)    
```

### script gets self name
```bash
basename "$(test -L "$0" && readlink "$0" || echo "$0")"
```

### System and resources
```bash
chkconfig --list|grep 1:on   # List all system service that is *on* on Level 1:
grep MemFree /proc/meminfo   # free memory
cat /etc/lsb-release         # head -n 1 /etc/issue* or *lsb_release -d*
lspci -tv                    # list all pci device
cat /proc/loadavg            # view system  average load
swapon -s                    # view swap
sudo iptables -L             # view firewall settings
id                           # view user id
last                         # login log
crontab -l                   # view jobs for current user
cut -d: -f1 /etc/passwd      # view all users
cut -d: -f1 /etc/group       # all groups
```

### change username

```bash
# logout from current username and log as root
su - username
kill -9 -1

# change username and directory
usermod -l new_username -d /home/new_username -m old_username

# change group
groupmod -n new_username old_username

# change fullname
chfn -f new_fullname new_username

# application settings
## firefox: `extension.ini` (might change, not for firefox 19 in my case)
## soft links inside home directory
## git submodules: need to deal with `.git/modules/`
## disks that mounted on some directory of home directory(`/etc/fstab`)
## auto login in LXDE: `/etc/lxdm/default.conf`
## remove auto generated folders in home directory

# sudo settings
username ALL=(ALL) NOPASSWD: ALL
```

### tr
```bash
## usage
# tr [OPTION] SET1 [SET2]  
# CHAR1-CHAR2,[CHAR*],[CHAR*REPEAT],[:alnum:],[:alpha:],[:blank:],[:cntrl:],[:digit:],[:graph:],[:lower:],[:print:],[:punct:],[:space:],[:upper:],[:xdigit:]

## interactive mode
tr abcdefghijklmnopqrstuvwxyz ABCDEFGHIJKLMNOPQRSTUVWXYZ
tr [:lower:] [:upper:]
tr a-z A-Z

## pipe mode
tr '{}' '()' < inputfile > outputfile
echo  -e "This\t is for or testing" | tr [:space:] | wc -l
echo "my number is 19890806" | tr -d [:digit:]
echo "my number is 19890806" | tr -cd [:digit:]
echo $PATH | tr ":" "\n"
```