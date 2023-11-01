---
public: true
---
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
