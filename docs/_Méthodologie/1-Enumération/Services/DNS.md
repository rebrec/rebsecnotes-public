---
public: true 
#Tags: tag1, tag2
---

Ports :  53 (tcp/udp)

Le port TCP/53 est utilisé dans les transferts de zone

Popular types of DNS attacks : <https://securitytrails.com/blog/most-popular-types-dns-attacks>

## Enumeration

```
nmap -p53 -Pn -sV -sC $TARGET
```

### Version

En plus de l'énumération classique nmap ou l'usage du script NSE dns-id, on peut parfois utiliser dig

```shell-session
dig CH TXT version.bind $TARGET
```

ou `host`

```
host -c CH -t TXT version.bind $TARGET
```

### Liste des serveurs DNS d'une Zone

```shell-session
# récupère la liste des nameserver depuis le serveur DNS $TARGET_IP
dig ns $DOMAIN @$TARGET
nslookup -query=NS $DOMAIN $TARGET
```

### Liste des enregistrements disponibles

```shell-session
dig any $DOMAIN @$TARGET
```

## Attaques / Exploitations

### Transfert de zone

```shell-session
dig axfr $DOMAIN @$TARGET | tee axfr_$DOMAIN
# create hostlist
cat axfr_$DOMAIN | grep -v ';' | tr "\t" " " | cut -d ' ' -f1 | sed 's/\.$//g' | tee found_axfr_subdomains.txt
```

### Bruteforce des sous domaines

#### Manuelle

```shell-session
for sub in $(cat /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.$DOMAIN @$TARGET | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```

##### dnsenum

```
$ dnsenum --dnsserver $TARGET_IP --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt $DOMAIN
```

##### gobuster

On peut brute forcer des sous domaines selon un motif (pattern)

```title="patterns.txt"
lert-api-shv-{GOBUSTER}-sin6
atlas-pp-shv-{GOBUSTER}-sin6
```

```shell title="exécution de gobuster"
export TARGET="facebook.com"
export NS="d.ns.facebook.com"
export WORDLIST="numbers.txt"
gobuster dns -q -r "${NS}" -d "${TARGET}" -w "${WORDLIST}" -p ./patterns.txt -o "gobuster_${TARGET}.txt"

Found: lert-api-shv-01-sin6.facebook.com
Found: atlas-pp-shv-01-sin6.facebook.com
Found: atlas-pp-shv-02-sin6.facebook.com
Found: atlas-pp-shv-03-sin6.facebook.com
Found: lert-api-shv-03-sin6.facebook.com
[...]
```

##### fierce

```
fierce --domain $DOMAIN --dns-servers $TARGET_IP --subdomain-file names.txt 
```

##### subfinder

```
# https://github.com/projectdiscovery/subfinder
./subfinder -d inlanefreight.com -v   
```

<https://github.com/aboul3la/Sublist3r>

##### subbrute

On peut utiliser subbrute pour trouver des sous domaines qui pourront ensuite resservir à trouver d'autres sous domaines.

Au lancement, subbrute essaie de faire une requête `ANY`. Si elle ne fonctionne pas, on obtiens une message : `Warning: No nameservers found, trying fallback list.`

Dans ce cas, on peut passer au domaine suivant.

```
# https://github.com/TheRook/subbrute
echo "rebrec.local" > ./resolvers.txt
./subbrute.py -s ../names.txt  $DOMAIN -r ../resolvers.txt -P
Warning: Fewer than 16 resolvers per process, consider adding more nameservers to resolvers.txt.
rebrec.local,SOA,ns1.rebrec.local. root.rebrec.local. 3 86400 3600 604800 3600
rebrec.local,NS,ns1.rebrec.local.
ns1.rebrec.local,A,127.0.0.1
www.rebrec.local,A,172.24.0.3
www1.rebrec.local,TXT,"Some Text !"
www2.rebrec.local,CNAME,www.rebrec.local.

```

## Découverte de sous domaines

Lorsqu'on cherche des sous domaines, on constate que le serveur DNS retourne des messages d'erreur différents selon si le domaine existe ou non.

Exemple avec un serveur DNS gérant la zone `inlanefreight.htb`

```
 host -t any aaa.inlanefreight.htb $TARGET_IP 
Using domain server:
Name: 10.129.243.143
Address: 10.129.243.143#53
Aliases: 

Host aaa.inlanefreight.htb not found: 3(NXDOMAIN)

```
