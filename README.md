# rgbCTF_writeup
Writeup for Name a more iconic band

Teammates who did most of this challenge - 
- Subhojeet Mukherjee
- Diptendu Khar 


This is a challenge from rgbCTF 2020 



### Challenge description -

```
Name a more iconic band

I'll wait.

The flag for this challenge is all the passwords in alphabetical order, each separated by a single white-space as an MD5 hash in lower case

md5(passwordA passwordB passwordC ...)

Example: if the passwords were "dog" and "cat", the flag would be
rgbCTF{md5("cat dog")}
rgbCTF{b89526a82f7ec08c202c2345fbd6aef3}
```



### There is a file attached with it named `data` (which you can find in this repository)

The file is a memory dump of a Windows computer

Therefore, we decided to open it with `Volatility`


### After installation of volatility the commands are as follows

- `vol.py -f ~/Downloads/data imageinfo`
     - This gave information about the dump such as the operating system version, size, etc.
     - It gave a list of operating systems it thought the dump came from
    
-  `volatility -f ~/Downloads/data --profile=Win7SP1x64 pslist` 
     - We selected the first in the list whhich was `Win7SP1x64`
     - The command above gives list of processes (& there PIDs) that were running at the moment when the dump was taken

- `vol.py -f ~/Downloads/data --profile=Win7SP1x64 memdump -p 1404 -D ~/Downloads/dump`

     - After getting PIDs from the last command we used the above command to get a memory dump of particular process. We tried doing it for iexplorer, notepad, etc.
     - Finally we did a memdump for `CMD` and there was a clue there
          - The cmd output was as follows 
          ```
          Radiohead
          
          !Where is the command radiohead
          ```
     - Radiohead was also appearing a lot of times in the HTML pages from memdum of iexplorer and notepad
     - The title of the challenge also looked like a clue ( Name a more iconic band)
     - Therefore, we did grep radiohead on the dump file and found some lines like 
          - `Radiohead\ok computer` which is a album by radiohead
     - So we did grep as follows 
          - `strings data | grep 'Radiohead\\' (double \ to because of escape character)
- We found 4-5 hits with pattern `Radiohead\{album name}`
     - They were user names of accounts on that machine
     
- Windows7 local passwords are stored in the following directory (NTLM hashed)
     - `Windows\System32\Config\SAM`
- We looked up this tutorial which gives information about how to extract [Volatility/Retrieve-password](https://www.aldeid.com/wiki/Volatility/Retrieve-password)

- `vol.py -f ~/Downloads/data --profile=Win7SP1x64 hivelist` 
     - This command gives virtual and physical memory addresses of hives
     
     (Sample example, not the one we used)
     ```
     $ ./vol.py -f ch2.dmp --profile=Win7SP1x86 hivelist
     Volatility Foundation Volatility Framework 2.4
     Virtual    Physical   Name
     ---------- ---------- ----
     0x8ee66740 0x141c0740 \SystemRoot\System32\Config\SOFTWARE
     0x90cab9d0 0x172ab9d0 \SystemRoot\System32\Config\DEFAULT
     0x9670e9d0 0x1ae709d0 \??\C:\Users\John Doe\ntuser.dat
     0x9670f9d0 0x04a719d0 \??\C:\Users\John Doe\AppData\Local\Microsoft\Windows\UsrClass.dat
     0x9aad6148 0x131af148 \SystemRoot\System32\Config\SAM
     0x9ab25008 0x14a61008 \SystemRoot\System32\Config\SECURITY
     0x9aba79d0 0x11a259d0 \??\C:\Windows\ServiceProfiles\LocalService\NTUSER.DAT
     0x9abb1720 0x0a7d4720 \??\C:\Windows\ServiceProfiles\NetworkService\NTUSER.DAT
     0x8b20c008 0x039e1008 [no name]
     0x8b21c008 0x039ef008 \REGISTRY\MACHINE\SYSTEM
     0x8b23c008 0x02ccf008 \REGISTRY\MACHINE\HARDWARE
     0x8ee66008 0x141c0008 \Device\HarddiskVolume1\Boot\BCD
     ```
- To get the username and hashes we used this command with `-y {virtual add of \REGISTRY\MACHINE\SYSTEM} ` and `-s { virtual add of \SystemRoot\System32\Config\SAM}
     - `vol.py -f ~/Downloads/data --profile=Win7SP1x64 hashdump  -y 0xfffff8a000023010 -s 0xfffff8a00105c010  > hashes.txt`
   

- We get usernames and NTLM hashes in `hashes.txt` file
