# Babylon State Sync

## Info
#### Public RPC endpoint: https://babylon-testnet.rpc.l0vd.com
#### Public API: https://babylon-testnet.api.l0vd.com

## Guide to sync your node using State Sync:

### Copy the entire command
```
sudo systemctl stop babylond
SNAP_RPC="https://babylon-testnet.rpc.l0vd.com"; \
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash); \
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.babylond/config/config.toml

peers="c2bc08c7b0292f7072b1530ffc03ebf69563f518@95.216.39.183:27656" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.babylond/config/config.toml 

babylond tendermint unsafe-reset-all --home ~/.babylond && sudo systemctl restart babylond && \
journalctl -u babylond -f --output cat
```

### Turn off State Sync Mode after synchronization
```
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1false|" $HOME/.babylond/config/config.toml
```
