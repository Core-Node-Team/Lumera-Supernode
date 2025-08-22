<h1 align="center"> Lumera




![image](https://github.com/user-attachments/assets/291dc80d-e97a-4941-b9be-ac0c39d2278c)

</h1>

 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>


<img width="462" height="285" alt="image" src="https://github.com/user-attachments/assets/63aff6f9-e99c-49e1-a47d-7887a0b644bd" />


### Portları açın
```
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 4444/tcp  # gRPC API
sudo ufw allow 8002/tcp  # REST Gateway
sudo ufw allow 4445/tcp  # P2P
```
### Dosyaları çek
```
sudo curl -L -o /usr/local/bin/supernode \
  https://github.com/LumeraProtocol/supernode/releases/latest/download/supernode-linux-amd64

sudo chmod +x /usr/local/bin/supernode
chmod +x sn-manager
sudo mv sn-manager /usr/local/bin/
supernode version
```
### Supernode init
```
supernode init --key-name myWalletSNKey --recover --chain-id lumera-testnet-2
```
↪️NOT: eğer yeni cüzdan oluşturmak istiyorsanız onla init edecekseniz yukarıdaki koddan --recover kısmını kaldırın. çıkan çıktıyıda kaydetmeyi unutmayın size kelimeler ve cüzdan vericek
↪️NOT: os file test die seçeneklerden testi seçin.kelimeleri sorunca yazın. grpc sorucak validaor nodunuzun grpcsini yazın sonu 90 lı olan (ip:port) açık değilse node sunucundan app.toml açın grpc die aratın false olan yer varsa başında ture ile değiştirin localhost:port yazıyorsa locakhost yerine 0.0.0.0 yazın kaydedin ve nodu baştan başlatın.

- validator sunucusundan vali adresi alma explorerdende bakabilirsiniz dabi. `lumerad keys show cüzdan-adini-yaz --bech val -a`
```
nano $HOME/.supernode/config.yml
```
↪️NOT: kontrol edelim eksik gidik olmasın
```
supernode:
    key_name: myWalletSNKey
    identity: BURADA CÜZDAN ADRESİNİZ YAZMALI
    ip_address: BURADA SUPERNODE SUNUCUSUNUN İP YAZMALI
    port: 4444
    gateway_port: 8002
keyring:
    backend: test
    dir: keys
p2p:
    listen_address: 0.0.0.0
    port: 4445
    data_dir: data/p2p
lumera:
    grpc_addr: BURADA GRPc SUNUCUSUNUN İP:PORT YAZMALI
    chain_id: lumera-testnet-2
raptorq:
    files_dir: raptorq_files
```

### Register
↪️ NOT: burda validator cüzdanımızla super node çalıştırmak istiyorsak yani mininmum 10k stake etmişsek kendimize.bunu validator sunucusunda çalıştırıcaz. ama baska adres verdiysek altaki 2 noduda dikkate alın yok vali cüzdanıyla kendinize 10k stake varsa alttaki 2 notu yapmanıza gerenk yok.

↪️NOT: baska bir cüzdan verdiyseniz adamlara ve na vestingli coin şey ettiyseler. öncelikle validator sunucusuna o cüzdanı import etmemiz gerekiyor. --from kısmınada o cüzdanı yazmak gerek. importtan sona o cüzdanla valimize de vestingli tokenleri delege etmek gerek bu kodla `lumerad tx staking delegate valoper-adresiniz 10000000000ulume --from adamlara-verilen-import-ettiğiniz-cüzdan-adi --gas auto --gas-adjustment 1.3 --fees 7000ulume --chain-id lumera-testnet-2`
    
↪️NOT: tabiki supernode yani yukarıdaki işlemlerde recovery ederkende adamlara verdiğiniz cüzdanın kelimelerini ve config.yaml içine onun adresini yazmanız gerekli.
```
lumerad tx supernode register-supernode \
  valoper-adresi Supernodeip:4444 supernode-cüzdanı \
  --from cüzdan-adi --chain-id lumera-testnet-2 \
  --gas auto --gas-adjustment 1.3 --fees 5000ulume
```
### Servis
```
sudo tee /etc/systemd/system/supernode.service <<EOF
[Unit]
Description=Lumera SuperNode
After=network-online.target

[Service]
User=$USER
ExecStart=/usr/local/bin/supernode start
Restart=always
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable --now supernode
```
```
journalctl -u supernode -f
```

```
sudo tee /etc/systemd/system/sn-manager.service <<EOF
[Unit]
Description=Lumera SuperNode Manager
After=network-online.target

[Service]
ExecStart=/usr/local/bin/sn-manager start
Restart=on-failure
RestartSec=10
LimitNOFILE=65536
WorkingDirectory=/root
Environment="HOME=/root"

[Install]
WantedBy=multi-user.target 
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable --now sn-manager
```
```
journalctl -fu sn-manager
```
