---
public: true
Tags: Lab
---

Basic DNS setup in a docker container to test your DNS enumeration tools for instance.

## Initial Setup

```bash
export DOMAIN=rebrec.local
mkdir dns_lab 
cd $_

cat << EOF > named.conf.local
zone "$DOMAIN" {
    type master;
    file "/etc/bind/db.$DOMAIN";
};
EOF

cat << EOF > db.$DOMAIN
\$TTL    1d ; default expiration time (in seconds) of all RRs without their own TTL value
@       IN      SOA     ns1.$DOMAIN. root.$DOMAIN. (
                  3      ; Serial
                  1d     ; Refresh
                  1h     ; Retry
                  1w     ; Expire
                  1h )   ; Negative Cache TTL

; name servers - NS records
     IN      NS      ns1.$DOMAIN.

; name servers - A records
ns1.$DOMAIN.        IN      A     127.0.0.1
www.$DOMAIN.        IN      A     172.24.0.3
www2.$DOMAIN.       IN      CNAME www
www1.$DOMAIN.       IN      TXT   "Some Text !"

EOF
```

## Edit DNS entries

Edit `db.$DOMAIN` file with your favorite text editor

## Run the DNS server

```bash
sudo docker run --name lab_bind --rm  -v $PWD/db.$DOMAIN:/etc/bind/db.$DOMAIN -v $PWD/named.conf.local:/etc/bind/named.conf.local --expose 53 -ti ubuntu/bind9 
```

## Test the DNS setup

In another terminal run the following :

```
# Store the container's IP address
export TARGET_IP=$(sudo docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' lab_bind) 


# Test Name resolution
host -t any www1.$DOMAIN $TARGET_IP
```

## Play around

You can then use your favorite DNS enumeration tools again the server `$TARGET_IP`
