---
public: true 
#Tags: tag1, tag2
---
Port : 873/tcp

Pentesting guide : https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync

## Enumeration 

```shell-session
sudo nmap -sV -p 873 127.0.0.1
```

## Tenter de lister des partages accessibles

Selon la configuration de rsync il est potentiellement possible de récupérer des données sans s'authentifier :

```shell-session
rebrec@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools       <============ ON A LE PARTAGE DEV QUI EST VISIBLE
@RSYNCD: EXIT
```

## Enumérer un partage ouvert

```shell-session
$ rsync -av --list-only rsync://127.0.0.1/dev         <== PARTAGE DEV

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```

## Télécharger le contenu du partage

```
rsync -av rsync://127.0.0.1/dev
```

Si rsync est configuré pour utiliser SSH pour transférer les fichiers, on peut ajouter `-e ssh` ou `-e "ssh -p1234" ` (voir https://phoenixnap.com/kb/how-to-rsync-over-ssh)
