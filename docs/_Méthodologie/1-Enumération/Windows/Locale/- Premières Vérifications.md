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
```

Selon les groupes et privilèges dont on dispose, on pourra potentiellement exploiter certains d'entre eux :

- [[Exploitation des privilèges Windows]]
- [[Exploitation des groupes Windows]]

```shell

# liste des utilisateurs
net user
# Liste des groupes
net localgroup

# politique de mot de passe
net accounts
```

[[Elements de protections]]

[[Système d'exploitation]]

[[Correctifs de Sécurité]]

- [[PrintNightmare]]
[[Firewall]]

[[Processus]] / services

```shell
# variables d'environnement
set
```

[[Services]]

[[Partages]]

[[Services en écoute]]

[[Infrastructure à clés publiqués (PKI)]]

[[Canaux nommés (Named Pipes)]]

[[Logiciels installés]]]]

[[Recherche de fichiers intéressants et credentials depuis Windows]]

[[Taches plannifiées]]

regarder les descriptions des utilisateurs AD

```powershell
 Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```

Références :

- <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands>
