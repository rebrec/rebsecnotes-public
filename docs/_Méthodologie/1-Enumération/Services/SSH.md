---
public: true 
#Tags: tag1, tag2
---

Port : 22/tcp

## Enumeration

#### SSH-Audit

```shell-session
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
./ssh-audit.py $TARGET_IP
```

## Change authentication method

```shell-session
ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

## Brute force avec hydra

```
 hydra -L user.list -P password.list ssh://$TARGET_IP
```
