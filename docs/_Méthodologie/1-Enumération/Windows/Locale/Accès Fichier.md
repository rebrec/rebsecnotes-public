---
public: true 
#Tags: tag1, tag2
---

```
accesschk.exe  -accepteula -uws "Tout le monde" "C:\Program Files"
accesschk.exe  -accepteula -uws "Everyone" "C:\Program Files"
accesschk.exe  -accepteula -uws "Users" "C:\Program Files"
accesschk64.exe  -accepteula -nobanner -wus "BUILTIN\Users" "c:\Department Shares"
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
