<img width="680" height="340" alt="image" src="https://github.com/user-attachments/assets/0198638d-6087-4ec5-b90a-dc305188db29" />

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
aztec-up latest
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

### Method 1: Run via Docker
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
    image: aztecprotocol/aztec:latest
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
Make sure your Sequencer node is fully synced, before you proceed with Validator registration.

**Official Validator Registration: ZKPassport**
* Visit: https://testnet.aztec.network/add-validator
* Complete ZKPassport humanity verification
* Follow the steps to connect your validator wallet and register your validator on the network
* After competion, you will get into the queue of validator registration and will earn **Explorer** discord role

---

## 12. Aztec Dashboard
* Visit the [Dashboard](https://dashtec.xyz/)
* Check your validators health
* Connect your X & Discord

---

## 🔃 Update Sequencer Node

### Update docker-compose method Nodes
1- Stop node
```console
docker stop $(docker ps -q --filter "ancestor=aztecprotocol/aztec") && docker rm $(docker ps -a -q --filter "ancestor=aztecprotocol/aztec")

# Or

cd aztec
docker compose down -v
```

2- Update CLI commands
```bash
source ~/.bashrc
aztec-up 1.1.2
```

3- Delete old data
```bash
rm -rf ~/.aztec/alpha-testnet/data/
```

4- Rerun your node
```
docker compose up -d
```
* Make sure you are in `aztec` directory where `docker-compose.yml` file exists.
* You can read the full details of running the node via [docker compose](#method-1-run-via-docker)

#

### Update CLI method Nodes
1- Stop node
```
screen -ls | grep -i aztec | awk '{print $1}' | xargs -I {} screen -X -S {} quit
```
* Or manually stop the screen session you are running your node in it

2- Update CLI commands
```bash
source ~/.bashrc
aztec-up 1.1.2
```

3- Delete old data
```bash
rm -rf ~/.aztec/alpha-testnet/data/
```

4- Rerun using this CLI command
```bash
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls RPC_URL  \
  --l1-consensus-host-urls BEACON_URL \
  --sequencer.validatorPrivateKey 0xYourPrivateKey \
  --sequencer.coinbase 0xYourAddress \
  --p2p.p2pIp IP
```
Replace the following variables before you Run the node:
* `RPC_URL` & `BEACON_URL`: Step 4
* `0xYourPrivateKey`: Your EVM wallet private key starting with `0x...`
* `0xYourAddress`: Your EVM wallet public address starting with `0x...`
* `IP`: Your server IP (Step 7)

---

## Run Multiple Validators
This step seems limited to only teams and individuals in active set. Team is encouraging teams to run 10 validators. Ask the team if you are going to run more validators

### Docker Method
1- Open `docker-compose.yml`
```
cd aztec
nano docker-compose.yml
```

2- Update private key:
* Update `VALIDATOR_PRIVATE_KEY: ${VALIDATOR_PRIVATE_KEY}` under `environment` with the following:
```
VALIDATOR_PRIVATE_KEYS: ${VALIDATOR_PRIVATE_KEYS}
```
* We added `s`

3- Add publisher key variable:
* Adding a publisher wallet will make you handle all the transactions of your validators with on wallet
* Add `SEQ_PUBLISHER_PRIVATE_KEY: ${SEQ_PUBLISHER_PRIVATE_KEY}` somewhere under `environment` in `docker-compose.yml`


4- Open `.env`
```
nano .env
```

5- Update private key:
* Update `VALIDATOR_PRIVATE_KEY` to `VALIDATOR_PRIVATE_KEYS`
* Values of `VALIDATOR_PRIVATE_KEYS` must be a comma (`,`) separated list. (`"0x123...,0x234...,0x345..."`)

6- Optional: Add publisher key variable:
`SEQ_PUBLISHER_PRIVATE_KEY`: The value of this is the privatekey of the wallet positng the transactions. This means you only need to fund sepETH to this wallet if you run multiple validators.

7- Register each validator on the network
* Do it manually or reach the team.

#

### CLI Method
* 1- Update your CLI start command to use `--sequencer.validatorPrivateKeys` (see added `s`) instead of `--sequencer.validatorPrivateKey` if you want to run multiple validators.
  * The value of this should be a comma (`,`) separated list.
   
* 2- Optional: Use `--sequencer.publisherPrivateKey` which will be the address the transactions are posted from. This means you only need to fund sepETH to this address if you run multiple validators.

Example:
```
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls RPC_URL  \
  --l1-consensus-host-urls BEACON_URL \
  --sequencer.validatorPrivateKeys "0xPrivatekey1,0xPrivatekey2,0xPrivatekey3" \
  --sequencer.publisherPrivateKey 0xPrivatekeyX
  --sequencer.coinbase 0xYourAddress \
  --p2p.p2pIp IP
```

* 3- Register each validator on the network
  * Do it manually or reach the team.

---

## Troubleshooting:
If you encountered: `ERROR: world-state:block_stream Error processing block stream: Error: Obtained L1 to L2 messages failed to be hashed to the block inHash`

* You have to stop your node, delete data and restart it.
* Follow [Update Node](https://github.com/0xmoei/aztec-network/blob/main/README.md#-update-sequencer-node) steps

---

## Get Guardian Discord Role:
Claim the Guardian role as you are running a Sequencer Node and keep an uptime.

* To claim:
  * Go to the `upgrade-role` channel
  * Type `/checkip`
  * Enter your `IP` & `Node Address`

* If you are not eligible to claim the Guardian role, wait until the next snapshot.

