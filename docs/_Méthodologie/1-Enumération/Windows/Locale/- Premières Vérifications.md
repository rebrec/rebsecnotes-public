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
whoami /priv
whoami /groups
net user Compromised_Account

# liste des utilisateurs
net user
# Liste des groupes
net localgroup

# politique de mot de passe
net accounts
```

[[Elements de protections]]

[[Système d'exploitation]]

[[Processus]] / services

```shell
# variables d'environnement
set

```

[[Services]]

[[Correctifs de Sécurité

[[Services en écoute]]

[[Canaux nommés (Named Pipes)]]

[[Logiciels installés]]]]

regarder les descriptions des utilisateurs AD

```powershell
 Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

Références :

- <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands>
 