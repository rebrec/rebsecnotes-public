---
public: true 
#Tags: tag1, tag2
---

```shell
# Liste des privilèges et informations AD
net user Compromised_Account
# Liste des groupes
net localgroup
```

regarder les descriptions des utilisateurs AD

```powershell
 Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

 
```