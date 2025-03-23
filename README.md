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

```
docker compose up -d
docker run --rm -v "$(pwd)/certbot/conf:/etc/letsencrypt" -v "$(pwd)/certbot/www:/var/www/certbot" certbot/certbot certonly --webroot -w /var/www/certbot -d tech19ws.moon.com --email tech.19@tech-19.com --agree-tos --no-eff-email
```