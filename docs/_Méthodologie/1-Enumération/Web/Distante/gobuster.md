---
public: true 
#Tags: tag1, tag2
---

```shell
# Pour toutes les commandes gobuster, on peut voir ce qui se passe en ajoutant 
# le paramètre :     --proxy http://127.0.0.1:8080

# Recherche de dossiers intéressant :
gobuster dir -u http://$TARGET:80/ -w "/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt" -t 50 --add-slash      
# extensions spécifiques


# Recherche d'enregistrements DNS 
gobuster dns -d $TARGET_VHOST -r $TARGET_IP -t 50 -w /usr/share/SecLists/Discovery/DNS/namelist.txt

# Recherche d'un sous domaine (VHOST)
gobuster vhost --append-domain --url $TARGET_VHOST -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -t 50 

``` 

Voir toutes les choses à énumérer avec [[ffuf]]
