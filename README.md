### 0GLABS-DA-NODE
<hr>

### Hardware Requirement
• Perating System: Ubuntu 18.04 or later LTS <br>
• Number of CPUs: 8 <br>
• RAM: 16GB <br>
• Storage: 1TB NVMe SSD <br>
• Bandwidth: 100mps for Download / Upload <br>
<hr>

### 1. Install dependencies
```
sudo apt-get update
sudo apt-get install clang cmake build-essential pkg-config libssl-dev protobuf-compiler llvm llvm-dev
```
<hr>

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
<hr>

### 3. Install rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"
rustc --version
```
<hr>

```
curl -L https://risczero.com/install | bash
source "$HOME/.cargo/env"
```
<hr>

```
source "/root/.bashrc"
```
<hr>

```
rzup install
```
<hr>

```
export PATH="$HOME/.cargo/bin:$PATH"
source "$HOME/.cargo/env"

echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```
<hr>

### 4. Download & build binary
```
git clone https://github.com/0glabs/0g-da-node.git
cd 0g-da-node
git fetch --all --tag
git checkout v1.1.3
git submodule update --init
cargo build --release
```
<hr>

### 5. Download params
```
./dev_support/download_params.sh
```
<hr>

### 6. Generate BLS private key
```
cargo run --bin key-gen
```
### Save Your BLS Key: 185656xxxxxxx
<hr>

### 7. Configuration
```
nano $HOME/0g-da-node/config.toml
```
### Copas this:
```
log_level = "info"

data_path = "./db/"
encoder_params_dir = "params/"
grpc_listen_address = "0.0.0.0:34000"
eth_rpc_endpoint = "https://rpc.ankr.com/0g_newton"
socket_address = "YOUR_IP_VPS:34000"
da_entrance_address = "0x857C0A28A8634614BB2C96039Cf4a20AFF709Aa9"
start_block_number = 940000
signer_bls_private_key = "BLS KEY"
signer_eth_private_key = "PK use Delegate"
miner_eth_private_key = "PK new Wallet"
enable_das = "true"
```
<hr>

### Note Replace your keys:<br>
• socket_address = YOUR_IP_VPS:34000 <br>
• signer_bls_private_key = 85656xxxxxxx (BLS KEY) <br>
• signer_eth_private_key = 4534xxxxxx (PK Address Delegate) <br>
• miner_eth_private_key = 4534xxxxxx (PK New Wallet)
<hr>

### 8. Create service
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
<hr>

### Note:
### BEFORE RUNNING YOUR NODE YOU MUST DELEGATE AT LEAST 31 A0GI
### Link Delegate:
https://0g.exploreme.pro/validators/
### ▫️  Connect Wallet Metamask
### ▫️  Delegate to Validator (31 A0GI)
### ✅ DONE, you can start Node
<hr>

### 9. Start node
```
sudo systemctl daemon-reload && \
sudo systemctl enable 0gda && \
sudo systemctl start 0gda && \
sudo systemctl status 0gda
```
<hr>

### 10. Check Logs
```
sudo journalctl -u 0gda -f -o cat
```
<hr>

### ✅ DONE!
![Screenshot 2025-03-27 092503](https://github.com/user-attachments/assets/d93837a2-4822-4429-be46-e21cc14dc293)
<hr>

### 11. IF NODE ENDED YOU CAN DELETE NODE USE COMMAND
```
sudo systemctl stop 0gda
sudo systemctl disable 0gda
sudo rm /etc/systemd/system/0gda.service
rm -rf $HOME/0g-da-node
```
<hr>
