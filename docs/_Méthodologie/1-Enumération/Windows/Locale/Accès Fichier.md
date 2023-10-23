---
public: true 
#Tags: tag1, tag2
---

```
accesschk.exe -uws "Tout le monde" "C:\Program Files"
accesschk.exe -uws "Everyone" "C:\Program Files"
accesschk.exe -uws "Users" "C:\Program Files"
# -u : supprimer les erreur
# -w : droit en écriture
# -s : récursif
```

```powershell
Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ? {$_.AccessToString -match "Everyone\sAllow\s\sModify"}
Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | ? {$_.AccessToString -match "Users\sAllow\s\sModify"}
```

```
# icacls

```
