---
public: true # set to true to make the article publishable
---

Lorsque l'on trouve un exploit prêt à l'emploi sur Internet et que cet exploit est conçu en tant que module metasploit il faut réaliser l'opération suivante pour qu'il soit disponible dans la console `msfconsole` :

## Création du dossier contenant les modules personnalisés

```
cd /root
mkdir -p /root/.msf4/modules/exploits/php/webapps/
cd $_
```

## Récupération de l'exploit

Pour notre exemple, on récupèrera l'exploit directement à partir de `searchsploit` :

```
#searchsploit -m 50064
  Exploit: Lightweight facebook-styled blog 1.3 - Remote Code Execution (RCE) (Authenticated) (Metasploit)
      URL: https://www.exploit-db.com/exploits/50064
     Path: /usr/share/exploitdb/exploits/php/webapps/50064.rb
File Type: Ruby script, ASCII text

Copied to: /root/.msf4/modules/exploits/php/webapps/50064.rb

```

## Mise à jour de la base de donnée MSF

Fermer la console msfconsole si elle est lancée puis lancer en tant que root :

```
updatedb
```

Patienter un moment

Puis relancer la console `msfconsole`

## Profit

Il ne reste plusp qu'à sélectioner son module depuis la console :

```

```
