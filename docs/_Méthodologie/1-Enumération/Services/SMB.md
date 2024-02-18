---
public: true
---

Ports : 135, 137, 139, 445

CIFS est une implémentation de SMB

Port CIFS : 445 exclusivement

On peut obtenir des informations sur des partages, des utilisateurs, des groupes, etc.

Voir aussi : [[docs/_Méthodologie/1-Enumération/Windows/Distante/Active Directory/Active Directory - SMB]]

Cheatsheet du SANS : <https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf>

```
$ sudo nmap $TARGET  -sV -sC -p139,445
```

## rpcclient

```
$ rpcclient -U "" $TARGET_IP 

Enter WORKGROUP\'s password:
rpcclient $> 
```

### commandes utiles

```
rpcclient $> srvinfo

        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
		
		
rpcclient $> enumdomains

name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]


rpcclient $> querydominfo

Domain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1


rpcclient $> netshareenumall

netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:
		
		
rpcclient $> netsharegetinfo notes

netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00 
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
                SID: S-1-1-0
```

### User enumeration

```
# user list
rpcclient $> enumdomusers

user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]

# user details
rpcclient $> queryuser 0x3e9

# user list WHEN enumdomusers is not allowed (bruteforce works generally)
$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done


```

### Group enumeration

```
# rpcclient
rpcclient $> enumdomgroups
[...]
group:[WebDevs] rid:[0x64e]

rpcclient $> querygroup 0x64e
        Group Name:     WebDevs
        Description:
        Group Attribute:7
        Num Members:1

rpcclient $> querygroupmem 0x64e
        rid:[0x647] attr:[0x7]

rpcclient $> queryuser 0x647
        User Name   :   C.Bum
        Full Name   :
        Home Drive  :
        Dir Drive   :
        Profile Path:
        Logon Script:
        Description :   Senior Web Developer
        Workstations:
[...]

# enum4linux
enum4linux -u $AD_USER -p "$AD_PASSWORD" -G $TARGET_IP 

```

## Impacket : samrdump.py

```shell-session
$ samrdump.py 10.129.14.128
```

## smbmap

```shell-session
# Auth Null Session
smbmap -H $TARGET

# Liste des partages
smbmap -u $AD_USER -p $AD_PASSWORD -H $TARGET_IP

# Liste de façon récursive les fichiers d'un partage (indique si les droits sont en écriture ou en lecture)
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP -R "$SHARE/"

#  Liste récusive d'un partage :
smbmap -u $AD_USER -p $AD_PASSWORD -d $AD_DOMAIN -H $TARGET_IP -r "$SHARE"

# Téléchargement d'un fichier 
smbmap -u "" -p "" -H $TARGET_IP --download  "sambashare\contents\flag.txt"

# Upload d'un fichier 
smbmap -u "" -p "" -H $TARGET_IP --upload file.txt "sambashare\file.txt"


```

### CrackMapExec

```shell-session
$ crackmapexec smb $TARGET_IP --shares -u '' -p ''
```

### enum4linux

```shell-session
# lots of output
$ enum4linux $TARGET -A
$ enum4linux-ng $TARGET -A
```

## smbclient

```
$ smbclient -N -L //$TARGET_IP
$ smbclient //$TARGET_IP/notes
```

### Téléchargement récursif d'un dossier

```shell
smbclient //$TARGET_IP/Share
RECURSE ON
PROMP OFF
mget *
```

## smbclient.py (impacket)

```
smbclient.py "$AD_USER:$AD_PASSWORD@$TARGET_IP"
smbclient.py "$AD_DOMAIN/$AD_USER:$AD_PASSWORD@$TARGET_IP"

# Affichage de l'arborescence d'un dossier
tree
```
