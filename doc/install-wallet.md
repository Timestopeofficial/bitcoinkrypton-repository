# Installation

## Recommended Installation Specifications

* **Ubuntu**: 22.04.xx

* **Node.js**: 18.xx

* **Python**: 3.10.xx

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

#### python
To check if `python3` is installed, Run:
```bash
sudo python3 --version
```
If `python3` is not installed on the server, Run:
```bash
sudo apt install python3 -y
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

#### nginx
to Install the `nginx`. Run:
```bash
sudo apt install nginx -y
```

## Download Source Code
Download the `bitcoin krypton wallet` source code. Run:
```bash
cd /var/www && sudo git clone https://github.com/Timestopeofficial/bitcoinkrypton-wallet.git
```

## Add Seed Peer

Open the `GenesisConfig.js` file. Run:
```bash
sudo vi /var/www/bitcoinkrypton-wallet/src/main/generic/consensus/GenesisConfig.js
```

Copy all the contents of the file in the link below.
- [`Seed Peers`](https://github.com/Timestopeofficial/bitcoinkrypton-repository/tree/main/seedNode/browser): The file at this link is a list of seed peer.

Paste the copied content under the phrase `TODO::`.
```
'main': {
    NETWORK_ID: 12,
    NETWORK_NAME: 'main',
    SEED_PEERS: [
        // TODO::
    ],
    SEED_LISTS: [],
    GENESIS_BLOCK: new Block(
        ...
    ),
    GENESIS_ACCOUNTS: 'AAA='
}
```

## Build
To build the `bitcoin krypton` source code. Run:
```bash
cd bitcoinkrypton-wallet && sudo yarn && sudo yarn build
```

If an ERROR occurs during the build, Run:
```bash
sudo yarn && sudo yarn build
```

Copy the built `dist` folder under `browser` folder, Run:
```bash
sudo cp -r dist clients/browser
```

## Add Service
Open `default` file. Run:
```bash
sudo vi /etc/nginx/sites-enabled/default
```

Change the `index` section in the file from `/var/www/html` to `/var/www/bitcoinkrypton-wallet/clients/browser`.
```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

To start the `bitcoinkrypton-wallet` service, Run:
```bash
sudo service nginx reload
```