---
public: true # set to true to make the article publishable
---


#### Cracker les hash :

```shell
hashcat -m 13100 --force -a 0 hash.txt dict.txt
```

## information sur les format de hash

```shell
$krb5tgs$23$* : RC4 (type 23) 
$krb5tgs$18$* : AES-256 (type 18)  (plus lent à casser)

```