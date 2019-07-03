# Enter Space-Time Coordinates
## Description
> Ok well done. The console is on. It's asking for coordinates. Beating heavily on the console yields little results, but the only time anything changes on your display is when you put in numbers.. So what numbers are you going to go for? You see the starship's logs, but is there a manual? Or should you just keep beating the console?

## Solution
We will begin by examining the attachment `00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed`.

```bash session
# check file type
$ file 00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed
00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed: Zip archive data, at least v2.0 to extract

# list contents of archive
$ unzip -l 00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed 
Archive:  00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed
  Length      Date    Time    Name
---------  ---------- -----   ----
      247  1980-00-00 00:00   log.txt
     8800  1980-00-00 00:00   rand2
---------                     -------
     9047                     2 files

# extract archive
$ unzip 00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed 
Archive:  00c2a73eec8abb4afb9c3ef3a161b64b451446910535bfc0cc81c2b04aa132ed
 extracting: log.txt                 
 extracting: rand2

# inspect log
$ cat log.txt 
0: AC+79 3888{6652492084280_198129318435598}
1: Pliamas Sos{276116074108949_243544040631356}
2: Ophiuchus{11230026071572_273089684340955}
3: Pax Memor -ne4456 Hi Pro{21455190336714_219250247519817}
4: Camion Gyrin{235962764372832_269519420054142}
# nothing interesting in the log

$ file rand2 
rand2: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/l, for GNU/Linux 3.2.0, BuildID[sha1]=0208fc60863053462fb733436cef1ed23cb6c78f, not stripped

# make program executable
$ chmod +x rand2

$ ./rand2 
Travel coordinator
0: AC+79 3888 - 28478299853782, 182971565615825
1: Pliamas Sos - 225837672517779, 150934844343973
2: Ophiuchus - 68497720409859, 41300755578706
3: Pax Memor -ne4456 Hi Pro - 255738478544440, 22618234158600
4: Camion Gyrin - 132037098862745, 99005105427072
5: CTF - <REDACTED>

Enter your destination's x coordinate:
>>> 0   
Enter your destination's y coordinate:
>>> 0
Arrived somewhere, but not where the flag is. Sorry, try again.
# output coordinates don't match log.txt . Does the program output change?

$ ./rand2 
Travel coordinator
0: AC+79 3888 - 25038303996304, 205841483468544
...
# yes, we are unlikely to be able to guess the correct coordinates

# lowtech flag search since the website shows that the flag format is CTF{...}
$ strings rand2 | grep CTF
Arrived at the flag. Congrats, your flag is: CTF{welcome_to_googlectf}
```

### Flag
`CTF{welcome_to_googlectf}`


## Bonus
TODO - Reverse the binary