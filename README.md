# Pharos-Validator-Node
Start with:
**1. Install docker** 
```sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**Test Docker** 
```sudo docker run hello-world

sudo systemctl enable docker
sudo systemctl restart docker
```

**##🛰️ Pharos Validator Node Deployment (Testnet)**
This guide walks you through setting up a **Pharos testnet node** using Docker and `docker-compose`.

# Preparation
**2. Prepare Working Directory** 
```bash
docker stop pharos-testnet && docker rm pharos-testnet

export WORKSPACE=testnet
```
**Optional: Backup snapshot**
```mv /data/$WORKSPACE/pharos-node/domain/light/data/public/ /data/
```

**Clean and recreate workspace** 
```rm -rf /data/$WORKSPACE
mkdir -p /data/$WORKSPACE
cd /data/$WORKSPACE
```

**Tip: If your data disk is mounted in /app, replace /data with /app accordingly:**
```mkdir /app/$WORKSPACE
```

**3. Create docker-compose.yml** 
Save the following in your $WORKSPACE directory:
```version: '3'

services:
  pharos:
    image: public.ecr.aws/k2g7b7g1/pharos/testnet:63b85b6b
    container_name: pharos-testnet
    volumes:
      - /data/$WORKSPACE:/data
    ports:
      - "18100:18100"
      - "18200:18200"
      - "19000:19000"
    restart: unless-stopped
```

**4. Start the Node** 
Run the following inside $WORKSPACE:
```docker-compose up -d
```
 or:
```docker compose up -d
```

**5. Wait for Sync** 
Check block sync after ~3 minutes:
```curl 127.0.0.1:18100/ \
  -X POST \
  -H "Content-Type: application/json" \
  --data '{"method":"eth_blockNumber","params":[],"id":1,"jsonrpc":"2.0"}'
```

**Other Commands** 
 Stop:
```docker-compose stop
```
Restart"
```docker-compose restart
```
Update image:
```docker-compose pull
```
Full refresh:
```docker-compose down && docker-compose up -d
```

