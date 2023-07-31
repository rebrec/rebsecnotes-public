---
public: true 
#Tags: tag1, tag2
---
Ports : 
- 512/tcp
- 513/tcp
- 514/tcp

## R-commandes

-   rcp (`remote copy`)
-   rexec (`remote execution`)
-   rlogin (`remote login`)
-   rsh (`remote shell`)
-   rstat
-   ruptime
-   rwho (`remote who`)

## Authentification

Il suffit qu'une entrée soit présente dans `/etc/hosts.equiv` ou dans `.rhosts` pour se voir accorder l'autorisation

```shell-session
$ cat /etc/hosts.equiv

# <hostname> <local username>
myremotehost rebrec
```

```shell-session
$ cat .rhosts

john     10.0.17.5               <=== l'hote 10.0.17.5 peut se loguer en tant que john
+               10.0.17.10       <=== peut se loguer en tant que n'importe quel user
+               +                 <=== n'importe qui peut se loguer ...
```

```shell-session
$ rlogin 10.0.17.2 -l michel

Last login: Fri Dec  2 16:11:21 from localhost

[michel@localhost ~]$
```


## Lister les utilisateurs connecté au serveur 

```shell-session
$ rwho

root     web01:pts/0 Dec  2 21:34
michel     workstn01:tty1  Dec  2 19:57  2:25 
```