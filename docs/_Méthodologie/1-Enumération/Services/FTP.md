---
public: true 
#Tags: tag1, tag2
---

Ports par défaut : 21, 20

## Méthodes de connexion

```
$ nc -nv $TARGET_IP 21
$ telnet $TARGET_IP 21
$ openssl s_client -connect "$TARGET_IP":21 -starttls ftp
```

## Commandes utiles

```shell
ftp> status


Connected to 10.129.14.136.
No proxy connection.
Connecting using address family: any.
Mode: stream; Type: binary; Form: non-print; Structure: file
Verbose: on; Bell: off; Prompting: on; Globbing: on
Store unique: off; Receive unique: off
Case: off; CR stripping: on
Quote control characters: on
Ntrans: off
Nmap: off
Hash mark printing: off; Use of PORT cmds: on
Tick counter printing: off
```

```shell
ftp> debug
Debugging on (debug=1).

ftp> trace
Packet tracing on.

ftp> ls
---> PORT 10,10,14,4,188,195
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
```

## Télécharger un dossier entier

```
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```

## Brute force avec hydra

```
 hydra  -l ceil -P rockyou.txt ftp://$TARGET_IP:2121 -t 48
```
