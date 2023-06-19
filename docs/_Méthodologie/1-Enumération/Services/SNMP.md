---
public: true 
#Tags: tag1, tag2
---
Ports
- 161 (agents)
- 162 (traps)

## Brute force de la communauté SNMP
```
onesixtyone -c dict.txt $TARGET_IP
```

## Parcours d'une partie de l'arborescence

### snmpwalk
```shell-session
snmpwalk -v 2c -c public $TARGET_IP 1.3.6.1.2.1.1.5.0
```
### braa
```
braa public@"$TARGET_IP":.1.3.6.*
```
