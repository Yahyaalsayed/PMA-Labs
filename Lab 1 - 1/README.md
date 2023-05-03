# Practical Malware Analysis - Lab Write-up 
<img src="Images/Cover%20--.jpeg"  width="90%" height="30%">



## Chapter 1 >  Basic Static Analysis

### lab 1- 1 Solution :
1. There is 54 from 70 engines marked lab01-01.exe as malicious, and there is 45 from 70 engines marked lab01-01.dll as malicious.

2. lab01-01.exe compilation time is 2010/12/19 16:16:19 UTC, and lab01-01.dll compilation time is 2010/12/19 16:16:38 UTC.

3. There are no indications that any file is packed or obfuscated.

4. Interesting lab01-01.dll imports as CreateProcess, Sleep and WS2_32.dll, and lab01-01.exe Imports KERNEL32.DLL and MSVCRT.DLL .

5. There is a file path C:\windows\system32\kerne132.dll , File named Lab01-01.DLL ,and some operations on files, kerne132.dll is a good host-based indicator in mentioned file Lab01-01.DLL .

6. There is a local IP address 127.26.152.13 .

7. The malware has 2 stages first stage Lab01-01.exe run or download Lab01-01.dll which is a backdoor.









-------------------------------------
### demonstrate lab01-01.exe :
File hash using [SHA256SUM.exe](http://www.labtestproject.com/files/win/sha256sum/sha256sum.exe) 
> 58898bd42c5bd3bf9b1389f0eee5b39cd59180e8370eb9ea838a0b327bd6fe47

then search  with this hash on [virustotal](https://www.virustotal.com/gui/home/upload)

![](Images/lab%2001-01/lab%2001-01%20exe/virustotal.png)
![](Images/lab%2001-01/lab%2001-01%20exe/VT%201.png)

we will find that 54 from 70 engines marked this file as malicious, and is detected as a trojan.

Get compilation date using [PEview](http://wjradburn.com/software/), check Time Data Stamp under IMAGE_FILE_HEADER under IMAGE_PE_HEADERS we find that the compilation date is 2010/12/19 Sun 16:16:19 UTC.

![](Images/lab%2001-01/lab%2001-01%20exe/PEview%20T%20D%20Stamp.png)

There is more than one way to check if the file packed or not 
* extract strings
* imports table
* size in memory during execution and  size in Hard Disk
* Tools


Using [EXEinfo](https://exeinfo-pe.en.uptodown.com/windows)

![](Images/lab%2001-01/lab%2001-01%20exe/Packing%20confirmation%20EXEinfo.png)


 Using  [DIE](https://github.com/horsicq/Detect-It-Easy) 
 
 ![](Images/lab%2001-01/lab%2001-01%20exe/entropy%20view.png)

 Using a very important feature in [DIE](https://github.com/horsicq/Detect-It-Easy) which is entropy (low entropy is a good indicator that the file isn't packed)

 size in memory and size in hard disk should be equal or there is a small difference, and we can check this by [PEview](http://wjradburn.com/software/) but this time in IMAGE_SECTION_HEADER .text in Virtual size box ( memory during execution ) and compare it's value with Size Of Raw Data ( size in hard disk ) and small difference between them is good indicator that the file isn't packed.

![](Images/lab%2001-01/lab%2001-01%20exe/size%20diff.png)

The last indicator that the file isn't packed is the large number of strings which are not available in packed file, we can extract strings using [Floss.exe](https://github.com/mandiant/flare-floss) or [Strings.exe](https://download.sysinternals.com/files/Strings.zip) 
<div>

<img src="Images/lab%2001-01/lab%2001-01%20exe/strings-1.png">
<img src="Images/lab%2001-01/lab%2001-01%20exe/strings-2.png">

</div>





Scanning the IMPORTS Table by [PEview](http://wjradburn.com/software/)

![](Images/lab%2001-01/lab%2001-01%20exe/import%20table.png)
We will find many intersting functions which refer that malware create files, copy files and search for specific files

**KERNEL32.DLL** 
* CreateFileA          
* FindFirstFileA
* FindNextFileA            
* CopyFileA

**MSVCRT.DLL**

There is some Host-Based Indicators found while extracting strings

![](Images/lab%2001-01/lab%2001-01%20exe/HBI.png)

calling **KERNEL32.DLL**  tells us that this malware can open and manipulate processes and files such as ReadFile, CreateFile, and WriteFile,
There is a Path of addional file C:\windows\system32\kerne132.dll ,and we will find a file called Lab01-01.DLL so we can guess that there is two stages Lab01-01.exe download or run another file called Lab01-01.dll .

After our analysis we can do a Compining analysis by [PEStudio](https://www.winitor.com/download)

![](Images/lab%2001-01/lab%2001-01%20exe/PE%201.png)

------------------------------

After analyzing Lab01-01.exe we must search for Lab01-01.DLL where is in the path we found and analyze it too.

------------------------------
### demonstrate lab01-01.dll :


Upload **lab01-01.dll** to [virustotal](https://www.virustotal.com/gui/home/upload) we will find that 45 from 70 engines marked this file as malicious 
![](Images/lab%2001-01/lab%2001-01%20dll/virus%20total.png)
![](Images/lab%2001-01/lab%2001-01%20dll/VT%201.png)

we can get time of compilation using [PEview](http://wjradburn.com/software/) then check Time Data Stamp under IMAGE_FILE_HEADER under IMAGE_PE_HEADERS
 ![](Images/lab%2001-01/lab%2001-01%20dll/PEview%20T%20D%20Stamp.png) we will find that the compilation time is 2010/12/19 Sun 16:16:38 UTC.

 when we extract strings by using [Floss.exe](https://github.com/mandiant/flare-floss) or [Strings.exe](https://download.sysinternals.com/files/Strings.zip) we will find a large number of strings and this is a good indicator that file isn't packed.

Using different tools to confirm that the file is packed or not and to show you the variety of ways.

My favourite tool is [EXEinfo](https://exeinfo-pe.en.uptodown.com/windows)

![](Images/lab%2001-01/lab%2001-01%20dll/Packing%20confirmation%20exeinfo.png) 
as shown it's confirmed that the file isn't packed.

 we can try [PEid](https://softfamous.com/peid/) 

![](Images/lab%2001-01/lab%2001-01%20dll/PEid.png)

Final tool we will use is [DIE](https://github.com/horsicq/Detect-It-Easy) in the entropy view which show that the file isn't packed .
![](Images/lab%2001-01/lab%2001-01%20dll/intropy%20view.png)
as shown file intropy is very low so the file isn't packed.

By the same mentioned tool  [PEview]() under SECTION .rdata there is IMPORT Address Table 

![](Images/lab%2001-01/lab%2001-01%20dll/import%20table.png)
another tool [Dependency Walker](https://www.dependencywalker.com/) 

![](Images/lab%2001-01/lab%2001-01%20dll/DW-1.png) 

There is a 3 IMPORTED libraries , KERNEL32.DLL : 

![](Images/lab%2001-01/lab%2001-01%20dll/DW-2.png)
There is interesting functions which hint that the malware **CreateProcess** then **Sleep** .

WS2_32.DLL :

![](Images/lab%2001-01/lab%2001-01%20dll/DW-3.png)

 as shown there is an interesting functions which refer that malware connect to unknown IP address ,and send and recieve packets.

 If we search in strings we will find this unknown IP .
 ![](Images/lab%2001-01/lab%2001-01%20dll/strings.png) 
 this IP 127.26.152.13 which is unmarked in [virustotal](https://www.virustotal.com/gui/home/upload) 

 ![](Images/lab%2001-01/lab%2001-01%20dll/IP.png)

 If we need **host-based indicators**

 ![](Images/lab%2001-01/lab%2001-01%20dll/str-2.png)

 this malware create mutex and open mutex (The mutexes can be used to prevent infection by the same malware in different instances), KERNEL32.DLL allow malware to open and manipulate processes and files such as ReadFile, CreateFile, and WriteFile.

There is an important **network-based indicator** :  IP 127.26.152.13 which malware connect to .

------------------------

**Finally** we found that the malware has 2 stages first stage is a trojan called Lab01-01.exe run or download Lab01-01.dll which is a backdoor located in the path we found. 