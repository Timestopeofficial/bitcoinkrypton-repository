# Installation
The pool server must have a domain setup.

- [`Source Code`](https://github.com/Timestopeofficial/bitcoinkrypton-pool): See here for source code downloads.

## Recommended Installation Specifications

* **Ubuntu**: 22.04.xx

* **Node.js**: 18.xx

* **PostgreSQL**: 14.xx

## Add and switch user

#### Add user
If you are new to Ubuntu (or Debian), we recommend using the user name `krypton` and the group name `sudo`. Run:
```bash
adduser --ingroup sudo --gecos "" krypton
```

#### Switch user
Switch to `krypton` user. Run:
```bash
su - krypton
```

## update OS
Update Ubuntu (or Debian). Run:
```bash
sudo apt update && sudo apt upgrade -y
```

## Install Framework

#### Node.js

To install `Node.js 18.x` version, Run:
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs -y
```

If you encounter an error while installing `Node.js 18.x`, you will need to reconnect to the server and switch to the `krypton` user. Run:
```bash
su - krypton
```

To check if the `Node.js 18.x` version installation is complete, Run:
```bash
node --version
```

## Install Module

#### build-essential
Install the compilation tool(`build-essential`). Run:
```bash
sudo apt install build-essential -y
```

#### yarn
to Install the `yarn`. Run:
```bash
sudo curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
sudo echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt install yarn -y
```

To check if the `yarn` installation is complete, Run:
```bash
yarn --version
```

If your server has `yarn` version as `0.xx+git` or has `cmdtest` installed, Run:
```bash
sudo apt remove cmdtest -y
sudo apt update
sudo apt install yarn -y
```

#### git
To check if `git` is installed, Run:
```bash
sudo git --version
```

If `git` is not installed on the server, Run:
```bash
sudo apt install git -y
```

#### PostgreSQL

to Install the `postgresql`. Run:
```bash
sudo apt install postgresql postgresql-contrib -y
```

To check if the `postgresql` installation is complete, Run:
```bash
psql --version
```

## Set up PostgreSQL
Exit from `krypton` user. Run:
```bash
exit
```

Switch to `postgres` user. Run:
```bash
su - postgres
```

Enter `PostgreSQL`. Run:
```bash
psql
```

Set a password for the `postgres` user. Run:
```bash
\password
```

Exit `PostgreSQL`. Run:
```bash
\q
```

Exit from `postgres` user. Run:
```bash
exit
```

Switch to `krypton` user. Run:
```bash
su - krypton
```

Open the `pg_hba.conf` file. Run:
```bash
sudo vi /etc/postgresql/14/main/pg_hba.conf
```

Change `peer` in the METHOD section to `md5`.
```
# Database administrative login by Unix domain socket
local   all             postgres                                peer

# TYPE  DATABASE        USER            ADDRESS                 METHOD
```

To restart the `postgresql` service, Run:
```bash
sudo systemctl restart postgresql
```

## Download Source Code
Download the `bitcoin krypton pool` source code. Run:
```bash
cd /home/krypton && sudo git clone https://github.com/Timestopeofficial/bitcoinkrypton-pool.git
```

## Build
Copy the `.env.sample` file to `.env` file. Run:
```bash
cd bitcoinkrypton-pool && sudo cp -r .env.sample .env
```

Open the `.env` file and be sure to change the password to an appropriate password! Run:
```bash
sudo vi .env
```

To install the `bitcoin krypton pool` modules. Run:
```bash
sudo yarn
```

To migrate the `bitcoin krypton pool` DB. Run:
```bash
sudo yarn migrate-db
```

## Set up Service
To add a path to the `tls` field in the `peer.conf` file, you need to grant the `Krypton` user permissions to the folder containing the SSL files.

#### Set up pool Service
Open `pool/peer.conf` file. Run:
```bash
sudo vi pool/peer.conf
```

Add value ​​to fields marked `TODO::`.
```
host: "TODO::", #Enter the domain you want to set up.
```

Add value ​​to fields marked `TODO::`.
```
tls: {
    cert: "TODO::", #Add the issued SSL cert path.
    key: "TODO::" #Add the issued SSL key path.
},
```

Add value ​​to fields marked `TODO::`.
```
wallet: {
    seed: "TODO::" #Add the wallet address(194 characters), It must be the same as the wallet registered in "pool/peer.conf".
},
```

Add value ​​to fields marked `TODO::`.
```
pool: {
    name: "Bitcoin Krypton Pool",
    address: "TODO::", #Add the wallet address(40 characters), It must be the same as the wallet registered in "payout/peer.conf".
    autoPayOutLimit: 5000000000, #Set a minimum distribution amount. Payment will be made if the accumulated mining amount more than this kSatoshi.
    poolFee: [2,0.2], #['from Block Number','Commission %'] <=> from 2nd block, 20% Commission / Ex) poolFee: [2,0.2, 1000001,0.1, 2000001,0.05]
}
```

Add value ​​to  marked `TODO::`.
```
poolServer: {
    enabled: true,
    port: 12711, # The port number of the pool server to which the miner will connect.
    sslCertPath: "TODO::", #Add the issued SSL cert path.
    sslKeyPath: "TODO::" #Add the issued SSL key path.
},
```

Copy all the contents of the link below.
- [`Seed Peers`](https://github.com/Timestopeofficial/bitcoinkrypton-repository/tree/main/seedNode/seed): The file at this link is a list of seed peer.


Add the copied content to the field marked `TODO::`.
```
seedPeers: [
    TODO::
]
```

#### Set up payout Service
Copy the `pool/peer.conf` file to `payout/peer.conf` Run:
```bash
sudo cp pool/peer.conf payout/peer.conf
```

Open `payout/peer.conf` file. Run:
```bash
sudo vi payout/peer.conf
```

Modify the values ​​of the fields as follows:
```
port: 12012, #Add a port for payout service.

...

poolServer: {
    enabled: false, #Deactivate service
    ...
},
poolService: {
    enabled: false #Deactivate service
},
poolPayout: {
    enabled: true #Activate service
},
```

## Add Service

#### Add pool service
Open `krypton.service` file. Run:
```bash
sudo vi /etc/systemd/system/krypton.service
```

Paste the contents below into `krypton.service` file.
```
[Unit]
Description=Krypton daemon
After=network-online.target

[Service]
Type=simple
Restart=always
RestartSec=1
User=krypton
WorkingDirectory=/home/krypton/bitcoinkrypton-pool/pool
ExecStart=/bin/bash /home/krypton/bitcoinkrypton-pool/pool/launch.sh
SyslogIdentifier=krypton
StartLimitInterval=0
LimitNOFILE=65536
LimitNPROC=65536

[Install]
WantedBy=multi-user.target
```

To enable the `krypton.service` service, Run:
```bash
sudo systemctl enable krypton.service
```

To start the `krypton.service` service, Run:
```bash
sudo systemctl start krypton.service
```

#### Add payout service

Add execute permissions to the `payout/launch.sh` file. Run:
```bash
sudo chmod +x payout/launch.sh
```

Open `crontab` file. Run:
```bash
crontab -e -u krypton
```

Copy the content below, this is the scheduler that will run the `payout` every hour.
```
0 */1 * * * /home/krypton/bitcoinkrypton-pool/payout/launch.sh
```

Paste the copied content into the `TODO::` field.
```
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
TODO::
```