# Lab 11-01 Analysis :

## Basic Analysis Using Pestudio : 
![alt text](Pestudio.png)

### Check Imports : 
> SizeofResource  
LockResource  
LoadResource  
FreeResource  
FindResourceA  
VirtualFree  
CreateFileA  

So it's obvious that is a **Dropper** 

### Check it's Sections using Resource Hacker : 
![alt text](Resources.png)
we find 32-Bit Executable file 

## Manual Code Review : 
### How Lab 11-01 Drop TGAD.bin : 

- 01 ) Call **FindResourceA**  API  which Return a Handle to specific Resourse’s information Block   
![alt text](<01 FindResource.png>)  
- 02 ) Call **LoadResource** API which take **FindResourceA** return value as Parameter & Return a Retrieved Handle to the data associated to a Resource    
![alt text](<02 LoadResource.png>)  
 - 03 ) Call **LockResource** API which take **LoadResource** return value as Parameter & Called to Optain a Pointer to the actual resource   
![alt text](<03 LockResource.png>)   
- 04 ) Call **SizeofResource** API which Determine the size of the Resource  
![alt text](SizeOfResource.png)   
 05) Call **VirtualAlloc** API which allocate space for dropped file    
![alt text](<05 VirtualAlloc.png>)  
- 06 ) Call **FreeResource** API which unmap allocated space  
![alt text](<06 FreeResource.png>)   
----
#### How Lab 11-01 perform Persistence : 
 ![alt text](Per_update_2.png)
#### XREFs for any API of them : 
![alt text](Pers_Update.png)
 we find that sample open specific registry key using **RegCreateKeyExA** with Regisrty subkey as parameter & **80000002h** referes to **HKEY_LOCAL_MACHINE**

> Registry Subkey : "SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"    

### Malware Stole credentials by GINA Interception which is  a dead technique.
