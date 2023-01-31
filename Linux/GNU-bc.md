---
title: GNU bc
description: 
published: true
date: 2023-01-31T15:02:49.121Z
tags: 
editor: markdown
dateCreated: 2023-01-31T15:02:49.121Z
---

##Why GNU bc
1. The need of CLI addicts or nerds
2. Enhancement to simple bash  integer operations like `echo $((16/2))`
3. More friendly than `dc` (which now even uses bc's library for arithmetic!)
4. Simple,arbitrary precision and quick
5. Results could be embeded into shell scripts easily using `` `<command>` `` or `$(<command>)`

##What can I do with it
1. Basic arithmetic and relational evaluation between expressions
2. Useful built-in fuctions and reserved words
3. Advanced computation together with math library
4. A C-like syntax for assignments,parentheses,comments and ***function definition and call***

##How to use it
```bash
SYNTAX: bc [ -hlwsqv ] [long-options] [  file ... ]
```
    
`-i`        Force interactive mode.  
`-l`        Define the standard math library.  
`-w`        Give warnings for extensions to POSIX bc.  
`-s`        Process exactly the POSIX bc language.  
`-q`        Do not print the normal GNU bc welcome(interactive mode).  
    
###1. Pipe Data Mode:
This is the most useful mode for me now and typical usage is like: `echo '<expression>'|bc`

###2. Interactive Mode:
Typing `bc` in your shell will lead you into this mode.
Some PSEUDO STATEMENTS are often used for this mode:  

```bash
limits(extension)   Print the local limits enforced by the local version of bc.  
quit                The bc processor will be terminated whenever a quit statement is read.  
last(extension)     last denotes a variable that has the value of the last printed number.  
```

Except for *quit*,we can also press `Ctrl+D` to exit.They can also be used for quick pipe data mode,however;for instance:  

```bash
$echo 'limits'|bc  
BC_BASE_MAX    = 2147483647
BC_DIM_MAX     = 16777215
BC_SCALE_MAX   = 2147483647
BC_STRING_MAX  = 2147483647
MAX Exponent   = 2147483647
Number of vars = 32767
```

###3. Batch Mode:
This mode usually comes with an input file and involves definition/call of functions.

##Some exmaples:
* Basic calculations:

```bash
(3*(24+4)/5-18)\%8          #output -2
a=0                         #yields a==0
b=a++                       #yields b==0,a==1
c=++a                       #yields c=2,a==2
0 && a++                    #output 0, yields a==2
!b || b--                   #output 1, yields b==0 
```

* Square root,pow and conversions:
    1. `sqrt` and `^` are often used with `scale`, which specifies the precision of the result.
    2. `obase` and `ibase` is used for the  conversions,this is usually used for quick conversion in pipe data mode

```bash
sqrt(2)                     #output 1
scale=3;sqrt(2)             #output 1.414
sqrt(-2.0)                  #yields Runtime error
 #used in shell
$echo "obase=17;ibase=16;FFF"|bc
```

* Advanced mathematical computation  
These operations demand `-l` option.Combination of these function will lead to many other powerful functions.  

Functions |   Meanings   |     Notes    |
:--------:|  :--------:  |:------------:|
s(x)      |   [;sin(x);] | x in radians
c(x)      |   [;cos(x);] | x in radians
a(x)      |   [;arc(x);] | return radians
l(x)      |   [;ln(x);]  | natural logarithm
e(x)      |   [;e ^x ;]  | exponential function
j(n,x)    |   [;J_n(x);] | Bessel function

```bash
scale=10;4/3*a(1)
1.0471975510
pi_3=last                   #yields $\pi$
e(l(2)/3)                   #yields $\sqrt[3]{2}$
```

* Function definition and call

```bash
define factorial(x) {
  if (x>1) {
    return (x * factorial(x-1))
  }
  return (1)
}
factorial(1000)             #output the value of 100!,see how fast it calculates!
```

* Read from streams like *file* and *stdin*
For instance,if the segment above is stored in the file `bc_file`,we can use
`bc bc_file`  or `cat bc_file |bc` to obtain the same result (note that the comments block starting with `#`). 
An stdin example can be like below:  

```bash
bc -q <<EOF
/*output the value of 6^(6^6)!*/ 6^6^6
EOF
```
Also things can be more complicated if we combine *bc* with more shell features.

* Other similar tools(to be listed...)
