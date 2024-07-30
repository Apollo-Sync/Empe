# Empe

Website: https://empe.io/team

X      : https://x.com/empe_io

Discord: updating...

System Requirements (Recommended)

CPU: 8 core

RAM: 16 GB

Storage(SSD): 200 GB SSD or Nvme

**Install required packages**
```
sudo apt update && sudo apt upgrade -y
sudo apt install make curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

**Install Go**
```
cd $HOME && \
ver="1.22.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
**Set vars**
```
MONIKER=<YOUR_MONIKER_NAME_GOES_HERE>
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export EMPE_CHAIN_ID="empe-testnet-2"" >> $HOME/.bash_profile
echo "export EMPE_PORT="43"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

**Download Binary**
```
cd $HOME
curl -LO https://github.com/empe-io/empe-chain-releases/raw/master/v0.1.0/emped_linux_amd64.tar.gz
tar -xvf emped_linux_amd64.tar.gz 
chmod +x emped
sudo mv emped ~/go/bin
```

**Config and Init App**
```
emped config node tcp://localhost:${EMPE_PORT}657
emped config keyring-backend test
emped config chain-id $EMPE_CHAIN_ID
emped init $MONIKER --chain-id $EMPE_CHAIN_ID
Add Genesis File and Addrbook
curl -Ls https://raw.githubusercontent.com/Apollo-Sync/empe/main/genesis.json
curl -Ls https://raw.githubusercontent.com/Apollo-Sync/empe/main/addrbook.json
```

**Configure Seeds and Peers**
```
SEEDS=""
PEERS="ce90310b1ff5f8d874b73953723906922c455152@65.109.62.115:20656,edfc10bbf28b5052658b3b8b901d7d0fc25812a0@193.70.45.145:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.empe-chain/config/config.toml
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001uempe\"/" $HOME/.empe-chain/config/app.toml
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.empe-chain/config/config.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.empe-chain/config/config.toml
```

**Config Pruning**
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.empe-chain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.empe-chain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.empe-chain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.empe-chain/config/app.toml
```

**Set Custom Port**
```
sed -i.bak -e "s%:1317%:${EMPE_PORT}317%g;
s%:8080%:${EMPE_PORT}080%g;
s%:9090%:${EMPE_PORT}090%g;
s%:9091%:${EMPE_PORT}091%g;
s%:8545%:${EMPE_PORT}545%g;
s%:8546%:${EMPE_PORT}546%g;
s%:6065%:${EMPE_PORT}065%g" $HOME/.empe-chain/config/app.toml
sed -i.bak -e "s%:26658%:${EMPE_PORT}658%g;
s%:26657%:${EMPE_PORT}657%g;
s%:6060%:${EMPE_PORT}060%g;
s%:26656%:${EMPE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${EMPE_PORT}656\"%;
s%:26660%:${EMPE_PORT}660%g" $HOME/.empe-chain/config/config.toml
```

**Set Service File**
```
sudo tee /etc/systemd/system/emped.service > /dev/null <<EOF
[Unit]
Description=empe-testnet
After=network-online.target

[Service]
User=$USER
ExecStart=$(which emped) start --home $HOME/.empe-chain
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

**Enable and Start Service**
```
sudo systemctl daemon-reload
sudo systemctl enable emped
sudo systemctl start emped && sudo journalctl -fu emped -o cat
```
**Wallet**
```
#Add new wallet
emped keys add wallet

#Recover Wallet
emped keys add wallet --recover

#List Wallet
emped keys list

#Delete Wallet
emped keys delete wallet

#Check Wallet Balance
emped q bank balances $(emped keys show wallet -a)
```

**Genesis & Addrbook**
```
curl -Ls https://raw.githubusercontent.com/Apollo-Sync/Airchains/main/genesis.json
curl -Ls https://raw.githubusercontent.com/Apollo-Sync/Airchains/main/addrbook.json
```
