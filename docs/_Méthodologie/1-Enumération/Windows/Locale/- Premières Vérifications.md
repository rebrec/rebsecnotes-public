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


## Eléments de protections en place
```powershell
# Etat de Windefender
Get-MpComputerStatus|select "*Enabled"
# Règles Applocker en place
Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
# Mode Powershell (Constrained Language Mode)
 $ExecutionContext.SessionState.LanguageMode
 
```