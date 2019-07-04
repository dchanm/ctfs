# Home Computer
## Description
Blunderbussing your way through the decision making process, you figure that one is as good as the other and that further research into the importance of Work Life balance is of little interest to you. You're the decider after all. You confidently use the credentials to access the "Home Computer." Something called "desktop" presents itself, displaying a fascinating round and bumpy creature (much like yourself) labeled "cauliflower 4 work - GAN post." Your 40 hearts skip a beat. It looks somewhat like your neighbors on XiXaX3. ..Ah XiXaX3... You'd spend summers there at the beach, an awkward kid from ObarPool on a family vacation, yearning, but without nerve, to talk to those cool sophisticated locals. So are these "Cauliflowers" earthlings? Not at all the unrelatable bipeds you imagined them to be. Will they be at the party? Hopefully SarahH has left some other work data on her home computer for you to learn more.

## Solution
The challenge includes the attachment `86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75`

```bash session
$ unzip -l 86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75
Archive:  86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75
  Length      Date    Time    Name
---------  ---------- -----   ----
 26214400  1980-00-00 00:00   family.ntfs
       49  1980-00-00 00:00   note.txt
---------                     -------
 26214449                     2 files

$ unzip 86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75
Archive:  86863db246859897dda6ba3a4f5801de9109d63c9b6b69810ec4182bf44c9b75
 extracting: family.ntfs
 extracting: note.txt

$ cat note.txt
If you're on MacOS, you can rename .ntfs to .dmg

$ file family.ntfs
family.ntfs: DOS/MBR boot sector, code offset 0x52+2, OEM-ID "NTFS    ", sectors/cluster 8, Media descriptor 0xf8, sectors/track 0, dos < 4.0 BootSector (0x80), FAT (1Y bit by descriptor); NTFS, sectors 51199, $MFT start cluster 4, $MFTMirror start cluster 3199, bytes/RecordSegment 2^(-1*246), clusters/index block 1, serial number 072643f694104cb6f

$ mkdir fs
$ sudo mount -o loop family.ntfs fs

$ ls -l fs
total 24
-rwxrwxrwx 1 root root     0 Jun 12 17:37 bootmgr
-rwxrwxrwx 1 root root     0 Jun 12 17:37 BOOTNXT
-rwxrwxrwx 1 root root     0 Jun 12 17:37 pagefile.sys
drwxrwxrwx 1 root root  4096 Jun 12 17:37 Program Files
drwxrwxrwx 1 root root  4096 Jun 12 17:37 Program Files (x86)
-rwxrwxrwx 1 root root     0 Jun 12 17:37 Setup.log
-rwxrwxrwx 1 root root     0 Jun 12 17:37 SSUUpdater.log
-rwxrwxrwx 1 root root     0 Jun 12 17:37 swapfile.sys
drwxrwxrwx 1 root root     0 Jun 12 17:37 Users
drwxrwxrwx 1 root root 16384 Jun 12 17:38 Windows
# interestingly most files appear to be 0 bytes

# search for files larger than 0 bytes
$ find fs -type f -size +1c
fs/Users/Family/Documents/credentials.txt

$ cat fs/Users/Family/Documents/credentials.txt
I keep pictures of my credentials in extended attributes.

# list Windows alternate data streams
$ getfattr fs/Users/Family/Documents/credentials.txt
# file: fs/Users/Family/Documents/credentials.txt
user.FILE0

# dump ADS
$ xattr -p user.FILE0 fs/Users/Family/Documents/credentials.txt > output
$ file output
output: PNG image data, 1234 x 339, 8-bit/color RGB, non-interlaced
```

Opening the PNG file reveals the flag

### Flag
`CTF{congratsyoufoundmycreds}`