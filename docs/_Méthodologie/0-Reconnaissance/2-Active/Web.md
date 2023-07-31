---
public: true 
#Tags: tag1, tag2
---

### En-têtes HTTP
```
curl -I "https://${TARGET}"

HTTP/1.1 200 OK
Date: Thu, 23 Sep 2021 15:10:42 GMT
Server: Apache/2.4.25 (Debian)
X-Powered-By: PHP/7.3.5
Link: <http://192.168.10.10/wp-json/>; rel="https://api.w.org/"
Content-Type: text/html; charset=UTF-8
```
### WhatWeb

```
whatweb -a3 https://www.facebook.com -v

```

### Wappalyzer 
L'installation de cette extension de navigateur permet d'obtenir un rapide aperçu des technologies utilisées sur un site web.


### Identification d'un WAF
Un Firewall applicatif peut parfois être identifié via l'utilitaire `wafw00f`

```
# installation
sudo apt install wafw00f -y

# utilisation
wafw00f -v https://www.tesla.com
```

### Obtenir des capture d'écran d'une liste de site web
Pour obtenir un appercu d'une liste de sites, on peut utiliser `aquatone

```
# installation 
sudo apt install golang chromium-driver
go get github.com/michenriksen/aquatone
export PATH="$PATH":"$HOME/go/bin"

# utilisation
cat facebook_aquatone.txt | aquatone -out ./aquatone -screenshot-timeout 1000
```

