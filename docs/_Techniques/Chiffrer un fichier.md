---
public: true # set to true to make the article publishable
---

Lorsqu'on doit télécharger une donnée sensible il est nécessaire de la chiffre

```powershell
# Télécharger le fichier https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1

Import-Module .\Invoke-AESEncryption.ps1

Invoke-AESEncryption.ps1 -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
```

```shell
# chiffrement
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc

# déchiffrement
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd     
```
