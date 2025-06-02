## 🔃 Update Sequencer Node
The Docker image we use is typically `alpha-testnet:latest`. However, the team may occasionally recommend switching to a downgraded version until the latest one stabilizes. Be sure to monitor Discord for the latest updates.
In this update, I've adjusted the node to a specific downgraded version (`aztecprotocol/aztec:0.87.2`) until the `latest` version becomes stable

### 1- Stop Node:
```console
# CLI
docker stop $(docker ps -q --filter "ancestor=aztecprotocol/aztec") && docker rm $(docker ps -a -q --filter "ancestor=aztecprotocol/aztec")

screen -ls | grep -i aztec | awk '{print $1}' | xargs -I {} screen -X -S {} quit

# Docker
cd aztec
docker compose down
```

###  2- Update Node:
**`CLI` Nodes:**
```bash
aztec-up -v latest
```

**`Docker` Nodes:**
```bash
cd aztec
nano docker-compose.yml
```
* Replace `aztecprotocol/aztec:alpha-testnet` with `aztecprotocol/aztec:0.87.6`
* Add `      P2P_MAX_TX_POOL_SIZE: 1000000000` under `environment`:

![image](https://github.com/user-attachments/assets/c78b70be-f120-48d0-9723-59246ed20d68)


### 3- Re-run Node

Return to [Step 9: Run Sequencer Node](https://github.com/0xmoei/aztec-network/blob/main/README.md#9-run-sequencer-node) to re-run your node


### Notes:
* Wait for Discord update for the `latest` version stability.
* After update you can switch to `latest` version by:
  * `Docker`: Replace docker image with `aztecprotocol/aztec:alpha-testnet`
  * `CLI`: Enter command: `aztec-up alpha-testnet`
