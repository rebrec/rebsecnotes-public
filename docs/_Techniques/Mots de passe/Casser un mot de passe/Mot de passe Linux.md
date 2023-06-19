---
public: true # set to true to make the article publishable
---

```
unshadow /etc/passwd /etc/shadow > /tmp/unshadowed.hashes
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/unshadowed.cracked
```
