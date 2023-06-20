---
public: true 
#Tags: tag1, tag2
---
Ports : 
- 110 (POP3)
- 143 (IMAP avec ou sans TLS)
- 993 (IMAP (avec TLS)
- 995 (POP3 avec TLS)

## Enumeration 

### Via NMAP

```shell-session
sudo nmap $TARGET_IP -sV -p110,143,993,995 -sC
```

### Via cURL

```shell-session
curl -k 'imaps://$TARGET_IP' --user user:password -v
```

### Via OpenSSL

```shell-session
# POP3 over TLS
openssl s_client -connect $TARGET_IP:pop3s

# IMAP over TLS
openssl s_client -connect $TARGET_IP:imaps

```

## Commandes IMAP

Tutoriel : https://nickb.dev/blog/introduction-to-imap/

```
$ rlwrap -g LOGIN openssl s_client -connect $TARGET_IP:imaps -quiet -crlf


# Authentification
1 LOGIN username password

# Liste des boîtes aux lettres
1 LIST * * 

# Sélection d'une boîte aux lettres :
1 SELECT DEV.DEPARTMENT.INT
1 SELECT INBOX

# Recherche des messages présents :
1 UID SEARCH ALL
* SEARCH 1          <==== on a 1 mail dont l'UID est '1'

# Lecture d'un message dont on connait l'UID (1 ici)
#  (PEEK permet de ne pas marquer le message comme Lu)
1 FETCH 1 BODY.PEEK[HEADER]  <== affiche l'entête smtp
1 FETCH 1 BODY.PEEK[TEXT]    <== affiche le corps du mail
```

## Exploitation

### Enumération d'utilisateurs

```
telnet $TARGET_IP 110
[...]
USER baduser1

-ERR


USER existinguser

+OK
```