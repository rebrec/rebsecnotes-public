---
public: true # set to true to make the article publishable
---

```shell
$userHavingPrivsOnAnotherOne = "rebrec"
# creation d'une liste d'utilisateurs
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

# recher des privileges dont dispose $userHavingPrivsOnAnotherOne sur d'autres users
gc ad_users.txt | % {get-acl  "AD:\$(Get-ADUser $_)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match "$AD_DOMAIN\\$userHavingPrivsOnAnotherOne"}}
```

Les ACLs collectées référence un Guid dans la propriété `ObjectType`

On peut savoir à quoi il correspond via :

```shell
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl
```
