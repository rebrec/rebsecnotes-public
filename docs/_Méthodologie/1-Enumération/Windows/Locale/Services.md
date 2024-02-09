---
public: true # set to true to make the article publishable
---

Recherche de vecteur d'escalade via des services : [[Weak Permissions]]

```powershell
# Services démarrés ou non en dehors de c:\windows\system32
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
