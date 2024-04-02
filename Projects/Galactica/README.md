# Galactica Testnet guide

![gala](https://github.com/obajay/nodes-Guides/assets/44331529/f1c34bdf-9929-4c84-8e74-d0e5f0f68c84)

[WebSite](https://galactica.com/)\
[GitHub](https://github.com/Galactica-corp/galactica)
=
[EXPLORER](https://explorer.stavr.tech/Galactica-Testnet)
=

- **Аpproximate hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Testnet   |   4|  8GB | 150GB    |


# 1) Auto_install script
```python
wget -O galactict https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Galactica/galactict && chmod +x galactict && ./galactict
```

# 2) Manual installation

### Preparing the server
```python
sudo apt update && sudo apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

## GO 1.21.6
```python
ver="1.21.6"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

# Build 02.04.24
```python
cd $HOME && mkdir -p go/bin/
git clone https://github.com/Galactica-corp/galactica
cd galactica
git checkout v0.1.1
make install
```
*******🟢UPDATE🟢******* 00.00.23
```python
SOOON
```

`galacticad version --long | grep -e version -e commit`
- version: 
- commit: 

```python
galacticad init STAVR_guide --chain-id galactica_9301-1
galacticad config chain-id galactica_9301-1
```    

## Create/recover wallet
```python
galacticad keys add <walletname>
  OR
galacticad keys add <walletname> --recover
```

## Download Genesis
```python
wget -O $HOME/.galactica/config/genesis.json "https://raw.githubusercontent.com/Galactica-corp/networks/main/galactica_9301-1/genesis.json"

```
`sha256sum $HOME/.galactica/config/genesis.json`
+ d65d860b220328cb3ad4ddaa6bef90b763e81dcfe3a7204eef0779534acf7380

## Set up the minimum gas price and Peers/Seeds/Filter peers/MaxPeers
```python
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0agnet\"/;" ~/.galactica/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.galactica/config/config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.galactica/config/config.toml
seeds="2b590732225bca0f3f55db543861063c705bcd40@seed01-reticulum.galactica.com:26656,fe758700e25b59b6ba6e2784badcb6024ba1b760@seed02-reticulum.galactica.com:26656,028d8c875660f0e3fb1d893acd0b2220c619625f@seed03-reticulum.galactica.com:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.galactica/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.galactica/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.galactica/config/config.toml

```
### Pruning (optional)
```python
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.galactica/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.galactica/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.galactica/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.galactica/config/app.toml
```
### Indexer (optional) 
```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.galactica/config/config.toml
```

## Download addrbook
```python
wget -O $HOME/.galactica/config/addrbook.json "SOOOOOOOOON"
```

# Create a service file
```python
sudo tee /etc/systemd/system/galacticad.service > /dev/null <<EOF
[Unit]
Description=galacticad
After=network-online.target

[Service]
User=$USER
ExecStart=$(which galacticad) start --chain-id galactica_9301-1
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# StateSync Galactica Testnet
```python
SOOOOOOOOOON
```
# SnapShot Testnet updated every 5 hours  
```python
SOOOOOOOON
```

## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable galacticad
sudo systemctl restart galacticad && sudo journalctl -u galacticad -f -o cat
```

### Create validator
```python
galacticad tx staking create-validator \
--commission-rate 0.1 \
--commission-max-rate 1 \
--commission-max-change-rate 1 \
--min-self-delegation "1" \
--amount 1000000000000000000agnet \
--pubkey $(galacticad tendermint show-validator) \
--from <wallet> \
--moniker="STAVR_guide" \
--chain-id galactica_9301-1 \
--gas 300000 \
--identity="" \
--website="" \
--details="" -y
```

[🧩Services and Tools🧩](https://github.com/obajay/StateSync-snapshots/tree/main/Projects/Galactica)
=


## Delete node
```python
sudo systemctl stop galacticad
sudo systemctl disable galacticad
rm /etc/systemd/system/galacticad.service
sudo systemctl daemon-reload
cd $HOME
rm -rf galactica
rm -rf .galactica
rm -rf $(which galacticad)
```
#
### Sync Info
```python
galacticad status 2>&1 | jq .SyncInfo
```
### NodeINfo
```python
galacticad status 2>&1 | jq .NodeInfo
```
### Check node logs
```python
sudo journalctl -fu galacticad -o cat
```
### Check Balance
```python
galacticad query bank balances gala...addressjkl1yjgn7z09ua9vms259j
```


<h1 align="center"> 📚Useful commands📚 </h1>

# ⚙️Service

#### Info
```python
galacticad status 2>&1 | jq .NodeInfo
galacticad status 2>&1 | jq .SyncInfo
galacticad status 2>&1 | jq .ValidatorInfo
```
#### Check node logs
```python
sudo journalctl -fu galacticad -o cat
```
#### Check service status
```python
sudo systemctl status galacticad
```
#### Restart service
```python
sudo systemctl restart galacticad
```
#### Stop service
```python
sudo systemctl stop galacticad
```
#### Start service
```python
sudo systemctl start galacticad
```
#### reload/disable/enable
```python
sudo systemctl daemon-reload
sudo systemctl disable galacticad
sudo systemctl enable galacticad
```
#### Your Peer
```python
echo $(galacticad tendermint show-node-id)'@'$(wget -qO- eth0.me)':'$(cat $HOME/.galactica/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

# 🥅Working with keys

#### New Key or Recover Key
```python
galacticad keys add Wallet_Name
      OR
galacticad keys add Wallet_Name --recover
```
#### Check all keys
```python
galacticad keys list
```
#### Check Balance
```python
galacticad query bank balances gala...addressjkl1yjgn7z09ua9vms259j
```
#### Delete Key
```python
galacticad keys delete Wallet_Name
```
#### Export Key
```python
galacticad keys export wallet
```
#### Import Key
```python
galacticad keys import wallet wallet.backup
```

# 🚀Validator Management

#### Edit Validator
```python
galacticad tx staking edit-validator \
--new-moniker "Your_Moniker" \
--identity "Keybase_ID" \
--details "Your_Description" \
--website "Your_Website" \
--security-contact "Your_Email" \
--chain-id galactica_9301-1 \
--commission-rate 0.05 \
--from Wallet_Name \
--gas 350000 -y
```

#### Your Valoper-Address
```python
galacticad keys show Wallet_Name --bech val
```
#### Your Valcons-Address
```python
galacticad tendermint show-address
```
#### Your Validator-Info
```python
galacticad query staking validator galavaloperaddress......
```
#### Jail Info
```python
galacticad query slashing signing-info $(galacticad tendermint show-validator)
```
#### Unjail
```python
galacticad tx slashing unjail --from Wallet_name --chain-id galactica_9301-1 --gas 350000 -y
```
#### Active Validators List
```python
galacticad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
#### Inactive Validators List
```python
galacticad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
#### Check that your key matches the validator  (Win - Good.  Lose - Bad)
```python
VALOPER=Enter_Your_valoper_Here
[[ $(galacticad  q staking validator $VALOPER -oj | jq -r .consensus_pubkey.key) = $(galacticad status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\nYou win\n" || echo -e "\nYou lose\n"
```

#### Withdraw all rewards from all validators
```python
galacticad tx distribution withdraw-all-rewards --from Wallet_Name --chain-id galactica_9301-1 --gas 350000 -y
```
#### Withdraw and commission from your Validator
```python
galacticad tx distribution withdraw-rewards galavaloper1amxp0k0hg4edrxg85v07t9ka2tfuhamhldgf8e --from Wallet_Name --gas 350000 --chain-id=galactica_9301-1 --commission -y
```
#### Delegate tokens to your validator
```python
galacticad tx staking delegate Your_galavalpoer........ "100000000"agnet --from Wallet_Name --gas 350000 --chain-id=galactica_9301-1 -y
```
#### Delegate tokens to different validator
```python
galacticad tx staking delegate galavalpoer........ "100000000"agnet --from Wallet_Name --gas 350000 --chain-id=galactica_9301-1 -y
```
#### Redelegate tokens to another validator
```python
galacticad tx staking redelegate Your_galavalpoer........ galavalpoer........ "100000000"agnet --from Wallet_Name --gas 350000  --chain-id=galactica_9301-1 -y
```

#### Unbond tokens from your validator or different validator
```python
galacticad tx staking unbond Your_galavalpoer........ "100000000"agnet --from Wallet_Name --gas 350000 --chain-id=galactica_9301-1 -y
galacticad tx staking unbond galavalpoer........ "100000000"agnet --from Wallet_Name --gas 350000 --chain-id=galactica_9301-1 -y
```

#### Transfer tokens from wallet to wallet
```python
galacticad tx bank send Your_galaaddress............ galaaddress........... "1000000000000000000"agnet --gas 350000 --chain-id=galactica_9301-1 -y
```

# 📝Governance

#### View all proposals
```python
galacticad query gov proposals
```

#### View specific proposal
```python
galacticad query gov proposal 1
```

#### Vote yes
```python
galacticad tx gov vote 1 yes --from Wallet_Name --gas 350000  --chain-id=galactica_9301-1 -y
```
#### Vote no
```python
galacticad tx gov vote 1 no --from Wallet_Name --gas 350000  --chain-id=galactica_9301-1 -y
```
#### Vote abstain
```python
galacticad tx gov vote 1 abstain --from Wallet_Name --gas 350000  --chain-id=galactica_9301-1 -y
```
#### Vote no_with_veto
```python
galacticad tx gov vote 1 no_with_veto --from Wallet_Name --gas 350000  --chain-id=galactica_9301-1 -y
```