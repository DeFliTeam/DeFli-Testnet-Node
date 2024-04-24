

# DeFli TestNet Node



### Usage

1. Clone and set .env parameters

```bash
sudo git clone https://github.com/DeFliTeam/DeFli-Testnet-Node.git
```

```bash
cd node/
```

```bash
export CONDUIT_NETWORK=defli-chain-01-jerg8a427n
```


2. Download the required network configuration with:

```bash
sudo ./download-config.py $CONDUIT_NETWORK
```

3. Copy files

```bash
cp .env.example .env
```
Set the parameter OP_NODE_L1_ETH_RPC to
```bash
OP_NODE_L1_ETH_RPC=https://eth.nownodes.io/9007019c-841a-408b-9852-391f826db73e
```
Set the parameter OP_NODE_L1_ETH_RPC to 
```bash
OP_NODE_L1_BEACON=https://eth.nownodes.io/9007019c-841a-408b-9852-391f826db73e
```

4. Start the node!

```bash
docker compose up --build
```



5. You should now be able to `curl` your Conduit node:

```
curl -d '{"id":0,"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest",false]}' \
  -H "Content-Type: application/json" http://localhost:8545
```


You can map a local data directory for `op-geth` by adding a volume mapping to the `docker-compose.yaml`:

```yaml
services:
  geth: # this is Optimism's geth client
    ...
    volumes:
      - ./geth-data:/data
```



### Syncing

Sync speed depends on your L1 node, as the majority of the chain is derived from data submitted to the L1. You can check your syncing status using the `optimism_syncStatus` RPC on the `op-node` container. Example:

```
command -v jq  &> /dev/null || { echo "jq is not installed" 1>&2 ; }
echo Latest synced block behind by: \
$((($( date +%s )-\
$( curl -s -d '{"id":0,"jsonrpc":"2.0","method":"optimism_syncStatus"}' -H "Content-Type: application/json" http://localhost:7545 |
   jq -r .result.unsafe_l2.timestamp))/60)) minutes
```

### Network Stats

You can see how many nodes you are connected with the following command:

```
curl -d '{"id":0,"jsonrpc":"2.0","method":"opp2p_peerStats","params":[]}' \
  -H "Content-Type: application/json" http://localhost:7545
```
