---
public: true # set to true to make the article publishable
---

Format du fichier contenant les hash

```
SALTED_MD5_HASH$SALT:UTILISATEUR
```

## Usage avec John

```
john --wordlist=wordlist -format=dynamic='md5($s.$p)' md5hashsalted
```

## Usage avec Hashcat

```
hashcat -m 0 fichier.txt dictionnaire.txt
```
