---
public: true # set to true to make the article publishable
---
## Service SMTP / POP / IMAP

Il est intéressant de vérifier les méthodes autorisées par les serveur SMTP car les outils d'énumération ne prennent pas forcément en compte la détection de méthodes désactivées.

Après avoir vérifier les méthodes supportées, on pourra utiliser `smtp-user-enum` avec la méthode fonctionnelle (RCPT, VRFY, etc)

### Vérification des méthodes fonctionnelles

```
ncat --crlf  $TARGET_IP 25
220 TEST-DEV ESMTP
EHLO machin     <== il faut indiquer un nom d'hôte avant de pouvoir communiquer
250-TEST-DEV
250-SIZE 20480000
250-AUTH LOGIN PLAIN
250 HELP
HELP            <== /!\ même si on voit VRFY dans l'aide, il peut être désactivé !!
211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
VRFY aze
502 VRFY disallowed.

# Vérification du support de RCPT TO:
MAIL FROM:<rebrec@rebrec.com>
250 OK
RCPT TO: aze       <== /!\ message lorsqu'on ne précise pas le domaine
550 A valid address is required.
RCPT TO: aze@domain.com <== /!\ message lorsqu'on précise un domaine a relayer
530 SMTP authentication is required.
RCPT TO:aze@maildomain.com  <== /!\ message lorsqu'on précise le domaine de l'entreprise
550 Unknown user   <== la méthode RCPT TO fonctionne donc
```

```shell
# https://github.com/pentestmonkey/smtp-user-enum
/tools/smtp-user-enum/smtp-user-enum.pl -M RCPT -U userlist.txt -D maildomain.com -t $TARGET_IP

# without domain
smtp-user-enum -m VRFY -l rebrec -U /usr/share/seclists/Usernames/top-usernames-shortlist.txt  $TARGET 25  

# with domain

```

## Office 365

[O365spray](https://github.com/0xZDH/o365spray)

```
# Vérification de l'utilisation d'O365 pour le domaine cible
python3 o365spray.py --validate --domain $TARGET_DOMAIN

# User enumeration
python3 o365spray.py --enum -U users.list --domain $TARGET_DOMAIN

# Password spraying
python3 o365spray.py --spray -U validusers.txt -p 'P@ssw0rd' --count 1 --lockout 1 --domain $TARGET_DOMAIN
```
