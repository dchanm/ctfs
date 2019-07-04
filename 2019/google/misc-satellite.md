# Satellite
## Description
> Placing your ship in range of the Osmiums, you begin to receive signals. Hoping that you are not detected, because it's too late now, you figure that it may be worth finding out what these signals mean and what information might be "borrowed" from them. Can you hear me Captain Tim? Floating in your tin can there? Your tin can has a wire to ground control? Find something to do that isn't staring at the Blue Planet.

## Solution
We will begin by examining the attachment `768be4f10429f613eb27fa3e3937fe21c7581bdca97d6909e070ab6f7dbf2fbf`.

```bash session
# this challenge contains a zip archive similar to reversing-rand2
$ unzip -l 768be4f10429f613eb27fa3e3937fe21c7581bdca97d6909e070ab6f7dbf2fbf
Archive:  768be4f10429f613eb27fa3e3937fe21c7581bdca97d6909e070ab6f7dbf2fbf
  Length      Date    Time    Name
---------  ---------- -----   ----
  2866350  1980-00-00 00:00   README.pdf
  3315571  1980-00-00 00:00   init_sat
---------                     -------
  6181921                     2 files

# README.pdf contains some instructions and an image of a satellite with the text "Osmium"
$ ./init_sat 
Hello Operator. Ready to connect to a satellite?
Enter the name of the satellite to connect to or 'exit' to quit
osmium
Establishing secure connection to osmium
 satellite...
Welcome. Enter (a) to display config data, (b) to erase all data or (c) to disconnect

a
Username: brewtoot password: ********************       166.00 IS-19 2019/05/09 00:00:00        Swath 640km     Revisit capacity twice daily, anywhere Resolution panchromatic: 30cm multispectral: 1.2m        Daily acquisition capacity: 220,000km²    Remaining config data written to: https://docs.google.com/document/d/14eYPluD_pi3824GAFanS29tWdTcKxP_XUxx7e303-3E

^C

# the Google doc contains a Base64 string
$ echo 'VXNlcm5hbWU6IHdpcmVzaGFyay1yb2NrcwpQYXNzd29yZDogc3RhcnQtc25pZmZpbmchCg==' | base64 -d
Username: wireshark-rocks
Password: start-sniffing!
```

There doesn't appear to be any username / password prompt for the binary. Our next step should be to use Wireshark to sniff the packets when running the binary. We're assuming that the program will make a connection to a remote server either through its domain name or IP address. The Wireshark traffic shows a DNS request to
`satellite.ctfcompetition.com`. This domain name resolves to `34.76.101.29`. We can now filter the traffic by `ip.addr == 34.76.101.29`. If we right-click and select `Follow -> TCP Stream`, then we see the following
```
Username: brewtoot password: CTF{4efcc72090af28fd33a2118985541f92e793477f}
```

### Flag
`CTF{4efcc72090af28fd33a2118985541f92e793477f}`

## Bonus
TODO - Reverse the binary