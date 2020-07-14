# rgbCTF_writeup
Writeup for Name a more iconic band

Teammates who did most of this challenge - 
- Subhojeet Mukherjee
- Diptendu Khar 


This is a challenge from rgbCTF 2020 

### Challenge description -

Name a more iconic band

I'll wait.

The flag for this challenge is all the passwords in alphabetical order, each separated by a single white-space as an MD5 hash in lower case

md5(passwordA passwordB passwordC ...)

Example: if the passwords were "dog" and "cat", the flag would be
rgbCTF{md5("cat dog")}
rgbCTF{b89526a82f7ec08c202c2345fbd6aef3}


### There is a file attached with it named `data` (which you can find in this repository)

The file is a memory dump of a Windows computer

Therefore, we decided to open it with `Volatility`

After installation of volatility the commands are as follows

- `vol.py -f ~/Downloads/data imageinfo`
     - This gave information about the dump such as the operating system version, size, etc.
    
-  `volatility -f ~/Downloads/data --profile=Win7SP1x64 pslist` 

