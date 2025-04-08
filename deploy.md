# GWDG Deploy

```bash
git clone https://github.com/Mike-7777777/onyxG.git
cd onyxG/deployment/docker_compose
# force recreate, will lunch persistence databse and volume.
docker compose -f docker-compose.dev.yml -p onyx-stack up -d --build --force-recreate
```

.env config details:
https://docs.onyx.app/configuration_guide
`deployment/docker_compose/.env`

Auth type:
https://docs.onyx.app/configuration_guide#auth-type
oidc auth needs license.
google oatuh is free.

Consider use https://docs.gwdg.de/doku.php?id=en:services:general_services:academicid:start and hack the oidc in normal version.

How to keep it up-to-date:
```bash
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower \
  --interval 3600 onyx-stack-*
```

or put it into CI/CD:
```bash
- name: Deploy
  run: |
    ssh user@server "cd /opt/onyx && git pull && docker compose down && docker compose up -d --build"
```

For the instance, for <5000 users:
1. 16GB RAM
2. 8vCPU
3. 500G disk

m7g.xlarge

network: allow https traffic

install dependencies:
```bash
sudo yum update -y

sudo yum install docker -y
sudo service docker start

sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

sudo yum install git
```

If ubuntu:
```

sudo apt-get update -y
sudo apt-get upgrade -y

sudo apt install -y build-essential curl wget git vim tree htop net-tools software-properties-common apt-transport-https ca-certificates gnupg lsb-release unzip zip

git config --global user.name "Mike-7777777"
git config --global user.email "mike467901371@yahoo.com"

sudo apt update
sudo apt install -y python3-pip python3-venv

sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker

sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

sudo apt-get install git -y
```

put these into .env:
```
WEB_DOMAIN=<YOUR_DOMAIN>  # something like "onyx.app"

# if your email is something like "chris@onyx.app", then this should be "onyx.app"
# this prevents people outside your company from creating an account
VALID_EMAIL_DOMAINS=<YOUR_COMPANIES_EMAIL_DOMAIN>

AUTH_TYPE=basic
# if you want to enable email verification, uncomment the following
# REQUIRE_EMAIL_VERIFICATION=true
# SMTP_USER=<GMAIL_ACCOUNT_EMAIL_YOU_WANT_TO_SEND_VERIFICATION_EMAILS_WITH>
# SMTP_PASS=<GMAIL_ACCOUNT_PW_YOU_WANT_TO_SEND_VERIFICATION_EMAILS_WITH>

# if you've gone through the Google OAuth setup guide, then comment out
# the above and uncomment the following
# AUTH_TYPE=google_oauth
# GOOGLE_OAUTH_CLIENT_ID=
# GOOGLE_OAUTH_CLIENT_SECRET=
# SECRET=<RANDOMLY_GENERATED_UUID>

GEN_AI_MODEL_PROVIDER=openai
GEN_AI_MODEL_VERSION=gpt-4  # if it's an option, we recommend using gpt-4

# Default values here are what Postgres uses by default, feel free to change.
POSTGRES_USER=postgres
POSTGRES_PASSWORD=password
```

and .env.nginx:
```
DOMAIN=<YOUR_DOMAIN>  # something like "onyx.app"
```

get ssl:
```
./init-letsencrypt.sh
```

shut down

```
docker compose -f docker-compose.dev.yml -p onyx-stack down
```

