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

Selon les groupes et privilèges dont on dispose, on pourra potentiellement exploiter certains d'entre eux :

- [[Exploitation des privilèges Windows]]
- [[Exploitation des groupes Windows]]

[[Elements de protections]]

[[Système d'exploitation]]

[[Correctifs de Sécurité]]

==> [[PrintNightmare (CVE-2021-1675)]]

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

## Privesc

[Winpeas](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS) 

[PowerUp](https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerUp/PowerUp.ps1)

```powershell
Import-Module PowerUp.ps1
Invoke-AllCheck

```

SharpUp.exe audit

Seatbelt

[[Interactions avec des utilisateurs]]

Références :

- <https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands>
