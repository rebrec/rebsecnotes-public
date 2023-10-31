---
public: true # set to true to make the article publishable
---

Pour les commandes suivantes, privilégier les services écoutant sur 127.0.0.1 car ils sont rarement sécurisés correctement.

```shell
# liste des connections en écoute avec leur PID associé
netstat -ano | findstr "LIST"
```

Version powershell

```
PS C:\> Get-NetTCPConnection -State Listen | Select-Object -Property @{'Name' = 'ProcessName';'Expression'={(Get-Process -Id $_.OwningProcess).Name}},LocalPort

ProcessName                           LocalPort
-----------                           ---------
dfsrs                                     49731
dns                                       49694
services                                  49682
lsass                                     49674
lsass                                     49673
lsass                                     49667
svchost                                   49666
svchost                                   49665
wininit                                   49664
System                                    47001
Microsoft.ActiveDirectory.WebServices      9389
System                                     8000
System                                     5985
lsass                                      3269
lsass                                      3268
lsass                                       636
svchost                                     593
lsass                                       464
System                                      445
httpd                                       443
lsass                                       389
svchost                                     135
lsass                                        88
httpd                                        80
dns                                          53
dns                                          53
dns                                          53
dns                                          53
dfsrs                                     49731
dns                                       49694
services                                  49682
lsass                                     49674
lsass                                     49673
lsass                                     49667
svchost                                   49666
svchost                                   49665
wininit                                   49664
Microsoft.ActiveDirectory.WebServices      9389
lsass                                      3269
lsass                                      3268
lsass                                       636
svchost                                     593
httpd                                       443
lsass                                       389
System                                      139
svchost                                     135
httpd                                        80
dns                                          53
dns                                          53

```
