---
public: true 
#Tags: tag1, tag2
---

Si on a accès à des partages SMB, on peut monter le partage et utiliser `find` pour chercher des fichiers intéressants.

## Montage du partage SMB

```
mkdir -p /mnt/share
sudo mount -t cifs -o username=$AD_USER,password=$AD_PASSWORD,domain=$AD_DOMAIN //$TARGET_IP/Sharename /mnt/share
```

## Fichiers portant un nom intéressant

```

```

## Fichiers dont le contenu est intéressant

```
grep -rn /mnt/share -ie cred
```
