---
public: true
---

Article intéressant comparant le contenu des wordlist : <https://blog.sec-it.fr/en/2021/03/02/web-wordlists/>

Lorsqu'on ne connait ni le bon nom d'utilisateur, ni le mot de passe on pourra utiliser des wordlist combinant les couples utilisateur / mot de passe les plus connus (on utilisera l'option `-C wordlist.txt` avec [[Hydra]])

```shell
Seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 
```

Wordlist très courte pour tester des mots de passe communs : `/usr/share/metasploit-framework/data/wordlists/http_default_pass.txt`
