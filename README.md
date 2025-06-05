<div align="center">

# 👨🏻‍💻 **Pipe-Testnet-Node-Guide** 👨🏻‍💻

![image](https://github.com/user-attachments/assets/47cbe79e-e6ee-43e2-98cc-2543c524fe08)


</div>

# 🖥️ Device/System Requirements 


![Screenshot 2025-05-17 004130](https://github.com/user-attachments/assets/d5dbeabf-d78b-4d0c-a9dc-bd4cf77c3162)


# Pre-Requirements 🛠

* **You have to be Whitelist, if u have to run the Pipe testnet node-**

* If U alraedy rcvd the Mail then u can run the Pipe testnet-

* Fill this form if u have not rcvd the mail/invite code Yet- [Form](https://tinyurl.com/4435uukt)

# Install Require Dependecies

```
sudo apt-get update && sudo apt-get upgrade -y
```

* Other Packages

```
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev screen ufw -y
```

# Allow Incoming connections on Ports 

```
sudo ufw allow 22
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow ssh
sudo ufw enable
sudo ufw reload
```

# Applying Configurations

* To apply these settings, create a configuration file using these following command:

```
sudo bash -c 'cat > /etc/sysctl.d/99-popcache.conf << EOL
net.ipv4.ip_local_port_range = 1024 65535
net.core.somaxconn = 65535
net.ipv4.tcp_low_latency = 1
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.core.wmem_max = 16777216
net.core.rmem_max = 16777216
EOL'
sudo sysctl -p /etc/sysctl.d/99-popcache.conf
```

* Increase file limits for performance

```
sudo bash -c 'cat > /etc/security/limits.d/popcache.conf << EOL
*    hard nofile 65535
*    soft nofile 65535
EOL'
```


# Create Directories

```
sudo mkdir -p /opt/popcache
sudo mkdir -p /opt/popcache/logs
```


<div align="center">

# Download the binary 👨🏻‍💻

![image](https://github.com/user-attachments/assets/2a27c2de-5ccc-4e75-bc88-5047d4fa61f2)





</div>


# Download the binary 

* Open - [Download_Binary](https://download.pipe.network)

* Enter Invite Code that u have rcvd from pipe Mail

* Select the `Architecture` As per your vps or Device and Download it in your `Personal Computer`!



* 🔺 If u are using Vps then u have to move the binary file from your Local device to Vps by the Termius (SFTP) Feature!


<div align="center">

# Here are some steps, how u can move pop binary to vps 

</div>


* 1. Download the pop binary in your local pc

* 2. Open the termius application and move to SFTP 

* 3. Now, On your right screen - select the VPS which u want to send the file, left screen will be your local disk

* 4. Drag and drop your pop binary file to your Left screen To Right VPS's Home path. 

* 5. Now move & Unrap the pop file with following these commands!

```
sudo mv ~/pop-v0.3.2-linux-x64.tar.gz /opt/popcache/
```

```
cd /opt/popcache/
```

```
sudo tar -xzf pop-v0.3.2-linux-x64.tar.gz
sudo chmod +x ./pop
chmod 777 pop-v0.3.2-linux-x64.tar.gz
sudo ln -sf /opt/popcache/pop /usr/local/bin/pop
```


# Set Configuration File


```
sudo nano config.json
```

```
{
  "pop_name": "your-pop-name",
  "pop_location": "Your Location, Country",
  "invite_code": "Enter your Invite Code",
  "server": {
    "host": "0.0.0.0",
    "port": 443,
    "http_port": 80,
    "workers": 0
  },
  "cache_config": {
    "memory_cache_size_mb": 4096,
    "disk_cache_path": "./cache",
    "disk_cache_size_gb": 100,
    "default_ttl_seconds": 86400,
    "respect_origin_headers": true,
    "max_cacheable_size_mb": 1024
  },
  "api_endpoints": {
    "base_url": "https://dataplane.pipenetwork.com"
  },
  "identity_config": {
    "node_name": "your-node-name",
    "name": "Your Name",
    "email": "your.email@example.com",
    "website": "https://your-website.com",
    "discord": "your_discord_username",
    "telegram": "your_telegram_handle",
    "solana_pubkey": "YOUR_SOLANA_WALLET_ADDRESS_FOR_REWARDS"
  }
}
```

* Paste the following code in `config.json` File-

* Dont Forget to make all these changes in File! All are Important Configrations!

* `"pop_location":` Your VPS Region's Location


* `"memory_cache_size_mb":`  How much RAM memory u want to allocate to node! (In MB)....Like if u have 16 GB RAM then u can allocate 50-60% of total RAM!

* `"disk_cache_size_gb":` How much disk space do u want to allocate! (In GB) ....like if u have 500 GB free space in your disk then u can allocate around  200GB


# Create a Systemd Service File

```
sudo bash -c 'cat > /etc/systemd/system/popcache.service << EOL
[Unit]
Description=POP Cache Node
After=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/opt/popcache
ExecStart=/opt/popcache/pop
Restart=always
RestartSec=5
LimitNOFILE=65535
StandardOutput=append:/opt/popcache/logs/stdout.log
StandardError=append:/opt/popcache/logs/stderr.log
Environment=POP_CONFIG_PATH=/opt/popcache/config.json

[Install]
WantedBy=multi-user.target
EOL'
```

* Reload

```
sudo systemctl daemon-reload
```

* Enable

```
sudo systemctl enable popcache
```

* Start

```
sudo systemctl start popcache
```


# Managing Logs ⛓️‍💥

* Check node Status

```
sudo systemctl status popcache
```

![Screenshot 2025-05-27 190436](https://github.com/user-attachments/assets/719cce62-977c-4730-856a-c9325d316a09)


```
tail -f /opt/popcache/logs/stdout.log
```

* This will show something like that👇

![image](https://github.com/user-attachments/assets/e7e2eec0-a2fb-442f-8257-dbd9053c5a4d)



# Get pop_id & more info

```
curl -sk https://localhost/metrics | jq . && curl -sk https://localhost/state | jq . && curl -sk https://localhost/health | jq .
```

![image](https://github.com/user-attachments/assets/46e9cac6-66e8-4e37-83e1-f7dce5e9bb2f)



# Dashboard

https://dashboard.testnet.pipe.network/node/

* Add your `pop id` at the end of the link to get info about your node-

![Screenshot 2025-05-27 165755](https://github.com/user-attachments/assets/e6454400-a629-4fe8-806d-d0bce416a653)


# Stop the service 

```
sudo systemctl stop popcache
```

# Next day command for local pc

```
sudo systemctl restart popcache
```




<div align="center">

# 📈 Upgrade to new release/version (v0.3.2) {Local/Vps} 

![image](https://github.com/user-attachments/assets/0cec40ba-dbe3-4f53-92f8-f99b6104cc66)



</div


* Stop popcache service

```
sudo systemctl stop popcache
```

* Delete old binary 

```
sudo rm -f /usr/local/bin/pop
```

```
sudo rm -f /opt/popcache/pop-v0.3.0-linux-x64.tar.gz && sudo rm -f /opt/popcache/pop-v0.3.1-linux-x64.tar.gz
```

* Delete old logs data

```
sudo rm ~/opt/popcache/logs/stderr.log
```

```
sudo rm ~/opt/popcache/logs/stdout.log
```

* Now follow the [Download the binary](https://github.com/Mayankgg01/Pipe-Testnet-Node-Guide?tab=readme-ov-file#download-the-binary) process:

* After that Reload & Start the service:

```
sudo systemctl daemon-reload
```

```
sudo systemctl start popcache
```

* You dont need to do [Set Configuration File](https://github.com/Mayankgg01/Pipe-Testnet-Node-Guide?tab=readme-ov-file#set-configuration-file) process now:
 
* You can check the Logs and file status:

* if the logs looks like this: then its working:

![image](https://github.com/user-attachments/assets/258db6cb-1706-454b-96ec-e32ba35670a2)



<div align="center">

# 🏃 Path where u need to move `pop-v0.3.2-linux-x64.tar.gz` for LOCAL PC 🏃


</div


**------For the people who are facing issue while running pipe on local pc-- they are confused that where we have to move or drop the `pop-v0.3.2-linux-x64.tar.gz` file: So u have to watch this video for understing much batter:**

* **Process**: 

👉 Goto This Pc>Linux🐧>Ubuntu>Home>Your_wls_username>Paste_your_`pop-v0.3.2-linux-x64.tar.gz`_file

**Video Refrence-** 👇


https://github.com/user-attachments/assets/2a932bfc-df13-4c73-816a-f0297f244b92



* After paste the `pop-v0.3.2-linux-x64.tar.gz` file to your wsl path, **follow the process to unzip & move**:

 
```
sudo mv ~/pop-v0.3.2-linux-x64.tar.gz /opt/popcache/
```

```
cd /opt/popcache/
```

```
sudo tar -xzf pop-v0.3.2-linux-x64.tar.gz
sudo chmod +x ./pop
chmod 777 pop-v0.3.2-linux-x64.tar.gz
sudo ln -sf /opt/popcache/pop /usr/local/bin/pop
```

* **After that follow all steps from** [Set Configuration File](https://github.com/Mayankgg01/Pipe-Testnet-Node-Guide?tab=readme-ov-file#set-configuration-file)



If U have any issue then open a issue on this repo or Dm me on TG~


👉 Join TG for more Updates: https://telegram.me/cryptogg

Thank U! 👾

Happy Coding💗


