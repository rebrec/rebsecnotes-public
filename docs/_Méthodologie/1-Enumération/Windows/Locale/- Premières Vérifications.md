---
public: true
---

```shell
# interfaces réseaux, serveurs DNS, DHCP
ipconfig /all
# table de routage
route print
# table ARP
arp -a


# Liste des privilèges et informations AD
net user Compromised_Account
# Liste des groupes
net localgroup
```

[[Elements de protections]]

[[Système d'exploitation]]

[[Processus]] / services

```shell
# variables d'environnement
set


```

[[Services]]

regarder les descriptions des utilisateurs AD

```powershell
 Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

Références :

- <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands>
