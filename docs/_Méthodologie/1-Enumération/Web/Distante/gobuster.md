---
public: true # set to true to make the article publishable
---
```shell
# Pour toutes les commandes gobuster, on peut voir ce qui se passe en ajoutant 
# le paramètre :     --proxy http://127.0.0.1:8080

# Recherche de dossiers intéressant :
gobuster dir -u http://$TARGET_VHOST:80/ -w "/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt" -t 50 --add-slash
# extensions spécifiques


# Recherche d'enregistrements DNS 
gobuster dns -d $TARGET_VHOST -r $TARGET_IP -t 50 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Recherche d'un sous domaine (VHOST)
gobuster vhost --append-domain --url $TARGET_VHOST -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -t 50 

```