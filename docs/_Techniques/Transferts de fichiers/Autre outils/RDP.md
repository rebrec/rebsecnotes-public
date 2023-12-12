---
public: true # set to true to make the article publishable
---

On peut en général utiliser le copier / coller à travers une session RDP.

Lorsque cela ne fonctionne pas, on peut mapper un dossier sous forme de partage accessible depuis l'hote virtuel `\\tsclient`

```
# rdesktop
rdesktop 10.10.10.132 -d DOMAIN -u administrator -p 'Pass' -r disk:shareName='/tmp'

# xfreerdp
xfreerdp /v:10.10.10.132 /d:DOMAIN /u:administrator /p:'Pass' /kbd:0x40c /drive:share,/tools
```
