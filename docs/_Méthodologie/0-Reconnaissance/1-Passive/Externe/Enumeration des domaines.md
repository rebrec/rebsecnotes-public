---
public: true 
---

- Vérifier les certificats SSL : chercher d'éventuels noms de domaines alternatifs
- Récupérer les sous domaines connus de https://crt.sh/
	- `curl -s "https://crt.sh/?q=inlanefreight.com&output=json" | jq .`
	- Domaines uniques : `curl -s "https://crt.sh/?q=inlanefreight.com&output=json" | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u | tee subdomainlist` 
- Déterminer la liste des IP : 
	- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done`
	- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done | cut -d " " -f2 | sort -u | tee ip-addresses.txt`
- Voir les informations de ces hotes dans shodan : 
	- `for i in $(cat ip-addresses.txt);do shodan host $i;done`
- Récupération des enregistrements DNS disponibles :
	- `dig any inlanefreight.com`
- Récupération d'autres adresses 
	- `for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done`

## Ressources Cloud

**AWS** : requête google `intext:<company|domain> inurl:amazonaws.com`
**Azure** : requête google `intext:<company|domain> inurl:blob.core.windows.net`

[domain.glass](https://domain.glass/)

 [GrayHatWarfare](https://buckets.grayhatwarfare.com/)

