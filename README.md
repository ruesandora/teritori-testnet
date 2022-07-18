<h1 align="center">Teritori Testnet </h1>


# KURMAYIN FLOOD BITMEDI

### Selamlar, bugün Cosmos üzerinde olan Teritori Network'ün testnetine katılacağız, keyifli okumalar. 

*Telegram kanalı: https://t.me/TeritoriTurkish*

![image](https://user-images.githubusercontent.com/101149671/179496277-2037ebfb-a375-4979-a4ac-bcfcacff059c.png)

# Sistem gereksinimleri:

Not: Aslında 1 CPU bile yeterli olabilir ama benim tavsiyem bu CPU ve RAM değerleri:

```
2 CPU
2 RAM
80 GB SSD
```
# Makinemizi kuruyoruz:
```
apt update && apt upgrade -y 
```

```
apt install build-essential git curl gcc make jq -y
```

# Go kurulum:
```
wget -c https://go.dev/dl/go1.18.3.linux-amd64.tar.gz && rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz && rm -rf go1.18.3.linux-amd64.tar.gz
```

```
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
```

# Bu komutu girdikten sonra: "*Should return go version go1.18.3 linux/amd64*" yazıyorsa sıkıntı yoktur
```
go version
```

# Zinciri oluşturuyoruz:
```
git clone https://github.com/TERITORI/teritori-chain && cd teritori-chain && git checkout teritori-testnet-v2 && make install
```

# Kurulum gerçekleştiğni teyit etmek için (*Should return  teritori-testnet-v2-0f4e5cb1d529fa18971664891a9e8e4c114456c6*) yazıyorsa sıkıntı yok:
```
teritorid version
```

# Ağı başlatalım (<> işaretlerini kaldıralım)
```
teritorid init <VALİDATOR İSMİNİZ> --chain-id teritori-testnet-v2
```

# Yapılandırma dosyası: 
```
sed -i.bak 's/persistent_peers =.*/persistent_peers = "0b42fd287d3bb0a20230e30d54b4b8facc412c53@176.9.149.15:26656,2371b28f366a61637ac76c2577264f79f0965447@176.9.19.162:26656,2f394edda96be07bf92b0b503d8be13d1b9cc39f@5.9.40.222:26656"/' $HOME/.teritorid/config/config.toml
```

# Genesis dosyasını indirelim:
```
wget -O ~/.teritorid/config/genesis.json https://raw.githubusercontent.com/TERITORI/teritori-chain/main/testnet/teritori-testnet-v2/genesis.json
```

# Servis dosyası (dökümasyondan farklı olabilir bunu ben kendım oluşturdum sorunsuz çalışıyor):
```
tee <<EOF >/dev/null /etc/systemd/system/teritorid.service
[Unit]
Description=Teritori Cosmos daemon
After=network-online.target

[Service]
User=root
ExecStart=/root/go/bin/teritorid start
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

# Servisten sonra bu komutları:

```
systemctl enable teritorid
systemctl daemon-reload
systemctl restart teritorid
```

# Daha sonra loglara bakalım:
```
journalctl -u teritorid.service -f -n 100
```

# Eşleşme sorunu yaşarsa peer ekleyelim:
```
PEERS="0b42fd287d3bb0a20230e30d54b4b8facc412c53@176.9.149.15:26656,2f394edda96be07bf92b0b503d8be13d1b9cc39f@5.9.40.222:26656,8ce81af6b4acee9688b9b3895fc936370321c0a3@78.46.106.69:26656,2371b28f366a61637ac76c2577264f79f0965447@176.9.19.162:26656,2f394edda96be07bf92b0b503d8be13d1b9cc39f@5.9.40.222:26656,8ce81af6b4acee9688b9b3895fc936370321c0a3@78.46.106.69:26656,0b42fd287d3bb0a20230e30d54b4b8facc412c53@176.9.149.15:26656,2371b28f366a61637ac76c2577264f79f0965447@176.9.19.162:26656,8ce81af6b4acee9688b9b3895fc936370321c0a3@78.46.106.69:26656,0b42fd287d3bb0a20230e30d54b4b8facc412c53@176.9.149.15:26656,2371b28f366a61637ac76c2577264f79f0965447@176.9.19.162:26656,2f394edda96be07bf92b0b503d8be13d1b9cc39f@5.9.40.222:26656"
```

```
sed -i.bak -e "s/^seeds =./seeds = "$SEEDS"/; s/^persistent_peers =./persistent_peers = "$PEERS"/" $HOME/.teritorid/config/config.toml
SNAP_RPC="http://teritori.stake-take.com:26657/"
```

```
sudo systemctl restart teritorid
```

