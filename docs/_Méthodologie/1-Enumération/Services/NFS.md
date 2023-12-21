---
public: true
---

Ports : 111 (RPC), 2049 (NFS)

## Remarque

Si des partages sont découvert et que l'option `no_root_squash` a été utilisée, on pourra en tant que root, uploader des binaires setuid root ! Cela ne fonctionne pas si `root_squash` a été paramétré

## rpc info

```shell
rpcinfo inlanefreight.htb
nmap -sSUC -p111 192.168.10.1

# via un pivot 
proxychains -q nmap -n -Pn -sT -p 111 --script rpcinfo,nfs-showmount 172.16.8.20
```

## nmap

```
# enumeration de la version et du script rpcinfo
sudo nmap -Pn $TARGET_IP -p111,2049 -sV -sC

# usage de tous les scripts NFS (nfs-ls, nfs-showmount, nfs-statfs, rpcinfo)
sudo nmap --script nfs* $TARGET_IP -sV -p111,2049
```

## liste des partages NFS

```
$ showmount -e $TARGET_IP

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

## Montage d'un partage NFS

```
mkdir target-NFS
sudo mount -t nfs $TARGET_IP:/Remote_Share ./target-NFS/ -o nolock
cd target-NFS
```

## Démontage d'un partage NFS hors ligne

Si le serveur ne répond plus on pourra utiliser :

```
umount -f -l /mnt/monDossier
```

## Lister le contenu du dossier avec les UIDs et GIDs

```
$ ls -n 
total 16
-rw-r--r-- 1 1000 1000 1872 Sep 25 00:55 aze.priv
-rw-r--r-- 1 1000 1000  348 Sep 25 00:55 aze.pub
-rw-r--r-- 1    0 1000 1221 Sep 19 18:21 dds.sh
-rw-r--r-- 1    0    0 1872 Sep 19 17:27 id_rsa
```

## Obtenir le bon UID

```
$ sudo su -s /bin/bash <username>
```

### Création d'un utilisateur avec un UID spécifique

```
useradd -u 1234 <username>
passwd <username>
sudo su <username>
```

## Solution alternative : nfsshell

Cette solution permet d'émuler les uid et gid de notre choix

### installation

```
https://github.com/NetDirect/nfsshell
Tutorial : 
https://www.pentestpartners.com/security-blog/using-nfsshell-to-compromise-older-environments/
```


## Solution alternative (non testée ) :  NfSpy

https://github.com/bonsaiviking/NfSpy