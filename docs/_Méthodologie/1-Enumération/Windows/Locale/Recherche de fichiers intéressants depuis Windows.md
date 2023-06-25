---
public: true 
#Tags: tag1, tag2
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