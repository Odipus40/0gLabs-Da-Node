### 0gLabs-Da-Node

Hardware Requirement
Characteristic	Specification
Operating System	Ubuntu 18.04 or later LTS
Number of CPUs	8
RAM	16GB
Storage	1TB NVMe SSD
Bandwidth	100mps for Download / Upload


### 1. Install dependencies
```
sudo apt-get update
sudo apt-get install clang cmake build-essential pkg-config libssl-dev protobuf-compiler llvm llvm-dev
```

### 2. Install go
```
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

### 3. Install rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"
rustc --version
```
then
```
curl -L https://risczero.com/install | bash
source "$HOME/.cargo/env"
```
then
```
source "/root/.bashrc"
```

### 4. Install Rzup
```
export PATH="$HOME/.cargo/bin:$PATH"
source "$HOME/.cargo/env"

echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### 5. Download & build binary
```
git clone https://github.com/0glabs/0g-da-node.git
cd 0g-da-node
git fetch --all --tag
git checkout v1.1.3
git submodule update --init
cargo build --release
```

### 6. Download params
```
./dev_support/download_params.sh
```

### 7. Generate BLS private key
```
cargo run --bin key-gen
```
Save Your BLS Key

### 8. Configuration
```
nano $HOME/0g-da-node/config.toml
```
Copas this:
```
log_level = "info"

data_path = "./db/"
encoder_params_dir = "params/"
grpc_listen_address = "0.0.0.0:34000"
eth_rpc_endpoint = "https://rpc.ankr.com/0g_newton"
socket_address = "VPS_Public_IP:34000"
da_entrance_address = "0x857C0A28A8634614BB2C96039Cf4a20AFF709Aa9"
start_block_number = 940000
signer_bls_private_key = "BLS KEY"
signer_eth_private_key = "PK use Delegate"
miner_eth_private_key = "PK new Wallet"
enable_das = "true"
```
Replace your keys:
socket_address = VPS_Public_IP:34000
signer_bls_private_key = 85656xxxxxxx (BLS KEY)
signer_eth_private_key = 4534xxxxxx (PK Address Delegate)
miner_eth_private_key = 4534xxxxxx (PK New Wallet)

### 7. Create service
```
sudo tee /etc/systemd/system/0gda.service > /dev/null <<EOF
[Unit]
Description=0G-DA Node
After=network.target

[Service]
User=root
Environment="RUST_BACKTRACE=full"
Environment="RUST_LOG=debug"
WorkingDirectory=$HOME/0g-da-node
ExecStart=$HOME/0g-da-node/target/release/server --config $HOME/0g-da-node/config.toml
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
Note:
### BEFORE RUNNING YOUR NODE YOU MUST DELEGATE AT LEAST 31 A0GI
### Link Delegate:
https://0g.exploreme.pro/validators/

▫️ Connect Wallet Metamask
▫️ Delegate to Validator (31 A0GI)
✅ DONE, you can start Node

### 8. Start node
```
sudo systemctl daemon-reload && \
sudo systemctl enable 0gda && \
sudo systemctl start 0gda && \
sudo systemctl status 0gda
```

### 9. Check Logs
```
sudo journalctl -u 0gda -f -o cat
```
### ✅ DONE!

### 10. Delete node
```
sudo systemctl stop 0gda
sudo systemctl disable 0gda
sudo rm /etc/systemd/system/0gda.service
rm -rf $HOME/0g-da-node
```
