# Aztec Network Sequencer Node
A step by step guide on How to Run `Sequencer Node` on Aztec Network Testnet & Earn `Apprentice` Role.

* **What types of nodes can participate in the testnet?**
  * `Sequencer`: proposes blocks, validates blocks from others, and votes on upgrades.
  * `Prover`: generates ZK proofs that attest to roll-up integrity.

## Roles Info
Sequecner Nodes will receive a certain role for their contribution on Discord.

After running and syncing your Sequencer node, You can go through [Get Role](https://github.com/0xmoei/aztec-network/blob/main/README.md#get-apprentice-discord-role) step.

---

## Hardware Requirements
<table>
  <tr>
    <th colspan="3"> Sequencer Node HW Requirements </th>
  </tr>
  <tr>
    <td>RAM</td>
    <td>CPU</td>
    <td>Disk</td>
  </tr>
  <tr>
    <td><code>8-16 GB</code></td>
    <td><code>4-9 cores</code></td>
    <td><code>100+ GB SSD</code></td>
  </tr>
</table>

* **Prover Node**: Requiring ~40x machines with 16 cores and 128GB RAM
* I don't run `Prover` since it's for data-center computing systems, not me.

---

**Windows Users**: must install Ubuntu on Windows using this [guide](https://github.com/0xmoei/Install-Linux-on-Windows), then continue further steps.

**VPS Users**: can get started via a `VPS` with 4 cores CPU, 8GB RAM! [Purchase here](https://my.hostbrr.com/order/forms/a/NTMxNw==)

---

## 1. Install Dependecies
* Update packages:
```bash
sudo apt-get update && sudo apt-get upgrade -y
```

* Install Packages:
```bash
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```

* Install Docker:
```bash
sudo apt update -y && sudo apt upgrade -y
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

# Test Docker
sudo docker run hello-world

sudo systemctl enable docker
sudo systemctl restart docker
```

---

## 2. Install Aztec Tools
```bash
bash -i <(curl -s https://install.aztec.network)
```
```bash
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bashrc

source ~/.bashrc
```
* **Restart your Terminal** now to apply changes.
* Check if you installed successfully:
```bash
aztec
```

---

## 3. Update Aztec
```bash
aztec-up alpha-testnet
```

---

## 4. Obtain RPC URLs
* Find a 3rd party that supports Sepolia `RPC URL` & Sepolia `BEACON URL` APIs.
* Most of your usage is `RPC URL`. I recommend to use [Alchemy](https://dashboard.alchemy.com/) for `RPC URL` & Use [drpc](https://drpc.org/) for `Beacon URL`.
  
**More details on RPC solutions**:

### Get Your Own RPC by Running Geth & Prysm Nodes
* You can run your own local RPC nodes by following this guide: [geth-prysm-node](https://github.com/0xmoei/geth-prysm-node). You may need 600-1000 GB SSD
  
### Free:
* `RPC URL`: Create a Sepolia Ethereum HTTP API in [Alchemy](https://dashboard.alchemy.com/)
* `BEACON RPC`: Create an account on [drpc](https://drpc.org/) and search for `Sepolia Ethereum Beacon Chain ` Endpoints.

![image](https://github.com/user-attachments/assets/eae865ab-461f-46cd-b3f9-b7d118dcbbdf)

### Paid: 
For example: [Ankr](https://www.ankr.com/rpc/?utm_referral=LqL9Sv86Te) is supporting `RPC URL` & `Beacon URL`. You can Register, Fund it with a little USDT via your wallet, Create a project, get your normal **sepolia rpc** and **beacon sepolia rpc**.

![image](https://github.com/user-attachments/assets/cfde5dec-ac1a-4d58-855b-43c4374c5c87)

![image](https://github.com/user-attachments/assets/ffb97518-cd24-46ee-b131-92b2870ac407)

> You can run your own Geth & Prysm nodes to get your own `RPC URL` & `BEACON RPC` or find any other 3rd party solutions

---

## 5. Generate Ethereum Keys
Get an EVM Wallet with `Private Key` and `Public Address` saved.

---

## 6. Get Sepolia ETH
Fund your Ethereum Wallet with `ETH Sepolia`

---

## 7. Find IP
```bash
curl ipv4.icanhazip.com
```
* Save it

---

## 8. Enable Firewall & Open Ports
```console
# Firewall
ufw allow 22
ufw allow ssh
ufw enable

# Sequencer
ufw allow 40400
ufw allow 8080
```

---

## 9. Run Sequencer Node
You can run Sequencer Node through one of these two methods: `Docker` or `CLI`

### Method 1: Run via Docker (Recommended)
* Delete CLI Node
```bash
# Stop docker containers
docker stop $(docker ps -q --filter "ancestor=aztecprotocol/aztec") && docker rm $(docker ps -a -q --filter "ancestor=aztecprotocol/aztec")

# Stop screens
screen -ls | grep -i aztec | awk '{print $1}' | xargs -I {} screen -X -S {} quit
```

* Create `aztec` directory:
```bash
mkdir aztec
```

* Get into `aztec` directory:
```bash
cd aztec
```
 
* Create `.env`
```bash
nano .env
```

* Replace the following code in `.env`
```env
ETHEREUM_RPC_URL=RPC_URL
CONSENSUS_BEACON_URL=BEACON_URL
VALIDATOR_PRIVATE_KEY=0xYourPrivateKey
COINBASE=0xYourAddress
P2P_IP=P2P_IP
```
* Replace the following variables before you Run Node:
  * `RPC_URL` & `BEACON_URL`: Step 4
  * `0xYourPrivateKey`: Your EVM wallet private key starting with `0x...`
  * `0xYourAddress`: Your EVM wallet public address starting with `0x...`
  * `P2P_IP`: Your server IP (Step 7)


* Create `docker-compose.yml`:
```bash
nano docker-compose.yml
```

* Replace the following code in `docker-compose.yml`
```yml
services:
  aztec-node:
    container_name: aztec-sequencer
    network_mode: host 
    image: aztecprotocol/aztec:alpha-testnet
    restart: unless-stopped
    environment:
      ETHEREUM_HOSTS: ${ETHEREUM_RPC_URL}
      L1_CONSENSUS_HOST_URLS: ${CONSENSUS_BEACON_URL}
      DATA_DIRECTORY: /data
      VALIDATOR_PRIVATE_KEY: ${VALIDATOR_PRIVATE_KEY}
      COINBASE: ${COINBASE}
      P2P_IP: ${P2P_IP}
      LOG_LEVEL: debug
    entrypoint: >
      sh -c 'node --no-warnings /usr/src/yarn-project/aztec/dest/bin/index.js start --network alpha-testnet --node --archiver --sequencer'
    ports:
      - 40400:40400/tcp
      - 40400:40400/udp
      - 8080:8080
    volumes:
      - /root/.aztec/alpha-testnet/data/:/data
```
Note: My node data directory configued in `docker-compose.yml` is `/root/.aztec/alpha-testnet/data/`, yours can be anything.

* Run Node Docker:
```bash
docker compose up -d
```

* Node Logs:
 ```bash
docker compose logs -fn 1000
```

* Optional: Stop and Kill Node
```bash
docker compose down -v
```
* Done, you can now head to Step 10.


### Method 2: Run via CLI
* Open screen
```bash
screen -S aztec
```

* Run Node
```
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls RPC_URL  \
  --l1-consensus-host-urls BEACON_URL \
  --sequencer.validatorPrivateKey 0xYourPrivateKey \
  --sequencer.coinbase 0xYourAddress \
  --p2p.p2pIp IP
  --p2p.maxTxPoolSize 1000000000
```
Replace the following variables before you Run Node:
* `RPC_URL` & `BEACON_URL`: Step 4
* `0xYourPrivateKey`: Your EVM wallet private key starting with `0x...`
* `0xYourAddress`: Your EVM wallet public address starting with `0x...`
* `IP`: Your server IP (Step 7)

### Optional Commands:
**Screen Commands:**
* Minimze screen: `Ctrl` + `A` + `D`
* Return to screen: `screen -r aztec`
* Kill screen (when inside): `Ctrl`+`C+
* Kill screen (when outside): `screen -XS aztec quit`

---

## 10. Sync Node
After entering the command, your node starts running, It takes a few minutes for your node to get synced.

* Check the latest synced block number of your sequencer:
```
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r ".result.proven.number"
```
* Check the latest block number of Aztec network: https://aztecscan.xyz/

---

## 11. Register Validator
Make sure your Sequencer node is fully synced, before you proceed with Validator registration
```bash
aztec add-l1-validator \
  --l1-rpc-urls RPC_URL \
  --private-key your-private-key \
  --attester your-validator-address \
  --proposer-eoa your-validator-address \
  --staking-asset-handler 0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2 \
  --l1-chain-id 11155111
```
Replace `RPC_URL`, `your-validator-address` & 2x `your-validator-address`, then proceed

* Note: There's a daily quota of 5 validators registration per day, if you get error, try again tommorrow.
* If your Validator's Registration was successfull, you can check its stats on [Aztec Scan](https://aztecscan.xyz/validators)

---

## 12. Verify Node's Peer ID:
**Find your Node's Peer ID:**
```bash
sudo docker logs $(docker ps -q --filter ancestor=aztecprotocol/aztec:alpha-testnet | head -n 1) 2>&1 | grep -i "peerId" | grep -o '"peerId":"[^"]*"' | cut -d'"' -f4 | head -n 1
```
* This reveals your Node's Peer ID, Now search it on [Nethermind Explorer](https://aztec.nethermind.io/)
* Note: It might takes some hours for your node to show up in Nethermind Explorer after it fully synced.

---

## Sequencer/Validator Health Check
* Validator attestation stats:

https://t.me/aztec_seer_bot

![image](https://github.com/user-attachments/assets/04ca9f5d-ba72-43be-98ad-3601255000bf)

---

## 🔃 Update Sequencer Node
* 1- Stop Node:
```console
# CLI
docker stop $(docker ps -q --filter "ancestor=aztecprotocol/aztec") && docker rm $(docker ps -a -q --filter "ancestor=aztecprotocol/aztec")

screen -ls | grep -i aztec | awk '{print $1}' | xargs -I {} screen -X -S {} quit

# Docker
docker compose down
```

* 2- Update Node:
```bash
aztec-up alpha-testnet
```

* 3- Delete old data:
```bash
rm -rf ~/.aztec/alpha-testnet/data/
```

* 4- Re-run Node

Return to [Step 9: Run Sequencer Node](https://github.com/0xmoei/aztec-network/blob/main/README.md#9-run-sequencer-node) to re-run your node

---

## Troubleshooting:
If you encountered: `ERROR: world-state:block_stream Error processing block stream: Error: Obtained L1 to L2 messages failed to be hashed to the block inHash`

* You have to stop your node, delete data and restart it.
* Follow [Update Node](https://github.com/0xmoei/aztec-network/blob/main/README.md#-update-sequencer-node) steps

---

## Get Apprentice Discord Role:
Go to the discord channel :[operators| start-here](https://discord.com/channels/1144692727120937080/1367196595866828982/1367323893324582954) and follow the prompts, You can continue the guide with my commands if you need help.

![image](https://github.com/user-attachments/assets/90e9d34e-724b-481a-b41f-69b1eb4c9f65)

**Step 1: Get the latest proven block number:**
```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r ".result.proven.number"
```
* Save this block number for the next steps
* Example output: 20905

**Step 2: Generate your sync proof**
```bash
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params":["BLOCK_NUMBER","BLOCK_NUMBER"],"id":67}' \
http://localhost:8080 | jq -r ".result"
```
* Replace 2x `BLOCK_NUMBER` with your number

**Step 3: Register with Discord**
* Type the following command in this Discord server: `/operator start`
* After typing the command, Discord will display option fields that look like this:
* `address`:            Your validator address (Ethereum Address)
* `block-number`:      Block number for verification (Block number from Step 1)
* `proof`:             Your sync proof (base64 string from Step 2)

Then you'll get your `Apprentice` Role

![image](https://github.com/user-attachments/assets/2ae9ff7c-59ba-43ec-9a23-76ef8ccb997c)
