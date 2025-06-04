# Odoo Service

## Services
- ERP - Odoo

## Connect to EC2 Instance

```
ssh -i /path/key-pair-name.pem ec2-user@13.49.73.213
```

## Generate SSH Key For Server

```
ssh-keygen -t rsa
```

Copy this key and create a new one in you github account.

## Installations

* Install git
```
sudo yum install git -y
```

* Install Docker
```
sudo yum install docker -y
```

* Install docker compose plugin
```
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.33.1/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
```

Then run
```
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
```


## Install Services

Start docker with the command:
```
sudo systemctl start docker
```

Clone this repo and run the docker compose file.


## Get SSL Certificate

1. Install **Certbot**
```sh
sudo yum install python3-pip -y
sudo pip3 install certbot
```
2. Set the nginx config file to:
```
server {
    listen 80;
    server_name localhost;

    location /.well-known/acme-challenge/ {
        alias /var/www/certbot/.well-known/acme-challenge/;
    }
}
```

> To test this nginx config, create a file and try accessing it.

3. Run the command:
```sh
sudo certbot certonly --webroot -w www/certbot -d <your.domain.name>
```

4. Update the nginx config file:
```
server {
    listen 80;
    server_name tech19ws.mooo.com;

    # Redirect all HTTP traffic to HTTPS
    location / {
        return 301 https://$host$request_uri;
    }

    location /.well-known/acme-challenge/ {
        alias /var/www/certbot/.well-known/acme-challenge/;
    }
}

server {
    listen 443 ssl;
    server_name tech19ws.mooo.com;

    ssl_certificate /etc/letsencrypt/live/tech19ws.mooo.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/tech19ws.mooo.com/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://odoo:8069;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### Auto-Renewal
1. Open the crontab:
```
sudo crontab -e
```

2. Add this line:
```
0 3 * * * /usr/bin/certbot renew --quiet --post-hook "docker exec <nginx-container-name> nginx -s reload"
```
