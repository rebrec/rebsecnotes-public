---
public: true
---

Liste des processus avec le nom du service associé lorsqu'il est disponible

```
tasklist /SVC
```

```powershell
# liste des processus avec utilisateur et chemin d'accès
Get-WmiObject Win32_Process |  
   Select Name, @{Name="UserName";Expression={$_.GetOwner().Domain+"\"+$_.GetOwner().User}},@{Name="Path";E={$_.Path}} |
   Sort-Object UserName, Name
```
