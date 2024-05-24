## That's Not My Base

> Everyone knows about Base64, but do you know about this one?

Decoding the string from Base 92 gives the flag.

- FLAG - `flag{784454a9509196a33dba242c423c057a}`

## Twine

> Google tells me that twine means: "strong thread or string consisting of two or more strands of hemp, cotton, or nylon twisted together."

```
┌──(kali㉿kali)-[~/thm/nahamcon]
└─$ strings twine.jpg

o($F
2pP~
	Yt?
 p01`
flag{4ac54e3ba5f8f09049f3ad62403abb25}
```

- FLAG - `flag{4ac54e3ba5f8f09049f3ad62403abb25}`

## Uriel

> Uriel was browsing the web and he saw this big long blob of text in his address bar! He was telling me about it but I don't remember everything he said... I think he mentioned something like "it happened twice?" - `%25%36%36%25%36%63%25%36%31%25%36%37%25%37%62%25%33%38%25%36%35%25%36%36%25%36%35%25%36%32%25%33%36%25%33%36%25%36%31%25%33%37%25%33%31%25%33%39%25%36%32%25%33%37%25%33%35%25%36%31%25%33%34%25%36%32%25%33%37%25%36%33%25%33%36%25%33%33%25%33%34%25%36%34%25%33%38%25%33%38%25%33%35%25%33%37%25%33%38%25%33%38%25%36%34%25%36%36%25%36%33%25%37%64`

Double URL-decode will give the flag

- FLAG - `flag{8efeb66a719b75a4b7c634d885788dfc} `

## EICAR

> What is the MD5 hash of this file?
> Wrap the hexadecimal value in the flag{ prefix and { suffix to match the standard flag format.
> Note, your antivirus engine might flag this file -- don't worry, I promise it's not malware :)

Calculate the MD5 hash of the file to get the flag .

> FLAG - `flag{44d88612fea8a8f36de82e1278abb02f}`

## CopyPasta

> You know those funny Internet messages that everybody likes to copy and paste? Yeah....
> Turns out they make a good Warmups CTF challenge, too! 

```bash
┌──(kali㉿kali)-[~/thm/nahamcon]
└─$ nc challenge.nahamcon.com 31476
                                                                                
I'd just like to interject for a moment. What you're referring to as Linux, is  
in fact, GNU/Linux, or as I've recently taken to calling it, GNU plus Linux.    
Linux is not an operating system unto itself, but rather another free component 
of a fully functioning GNU system made useful by the GNU corelibs, shell        
utilities and vital system components comprising a full OS as defined by POSIX. 
                                                                                
Many computer users run a modified version of the GNU system every day, without 
realizing it. Through a peculiar turn of events, the version of GNU which is    
widely used today is often called Linux, and many of its users are not aware    
that it is basically the GNU system, developed by the GNU Project.              
                                          flag{1f68e019b29650f6e8ea15a7808f76fd}
There really is a Linux, and these people are using it, but it is just a part of
the system they use. Linux is the kernel: the program in the system that        
allocates the machine's resources to the other programs that you run. The kernel
is an essential part of an operating system, but useless by itself; it can only 
function in the context of a complete operating system. Linux is normally used  
in combination with the GNU operating system: the whole system is basically GNU 
with Linux added, or GNU/Linux. All the so-called Linux distributions are really
distributions of GNU/Linux!
```                                                                                                                         

- FLAG -   `flag{1f68e019b29650f6e8ea15a7808f76fd}`


