

# DeFli TestNet Node



### Usage

1. Select the network you want to run and set `CONDUIT_NETWORK` env variable. You will need to know the `slug` of the network. You can find this in the Conduit console. For public networks you can use the table above. Example:

```
# for Mode Mainnet
```bash
export CONDUIT_NETWORK=defli-chain-01-jerg8a427n
```

Note: The external nodes feature must be enabled on the network for this to work. For the public networks above this is already set.

2. Download the required network configuration with:

```bash
sudo ./download-config.py $CONDUIT_NETWORK=defli-chain-01-jerg8a427n
```

3. Copy files

```bash
cp .env.example.celestia .env
```

4. Start the node!

```bash
docker compose up --build
```


```bash
docker compose -f docker-compose.celestia.yml up --build
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
