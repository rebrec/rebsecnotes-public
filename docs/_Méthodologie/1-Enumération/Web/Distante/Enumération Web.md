---
public: true 
#Tags: tag1, tag2
---

## Certificats

```
echo | openssl s_client -showcerts -servername <domaine> -connect <domaine>:443 2>/dev/null | openssl x509 -inform pem -noout -text
```

## Robots.txt

## Source Code
