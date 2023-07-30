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




##### Cracker les hash (bruteforce)

```
john --wordlist=/path/to/rockyou.txt hashes.txt
```
[[_Techniques/Mots de passe/Casser un mot de passe/Hashcat]]
