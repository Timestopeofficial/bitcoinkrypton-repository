# Add Node Miner
In order to mine on a node, the node must be fully synchronized to the latest block.

To install seed, refer to the link below.
- [`Install Seed`](https://github.com/Timestopeofficial/bitcoinkrypton-seed/blob/main/doc/INSTALL.md): This link is for instructions on how to install the seed.

## Add miner function

Open `peer.conf` file. Run:
```
vi /home/krypton/bitcoinkrypton-seed/rpc/peer.conf
```

Copy all the contents of the below.
```
wallet: {
    address: "0000111122223333444455556666777788889999",
},
miner: {
    enabled: "yes",
}
```

After adding the copied content as shown below, please enter the wallet address you wish to receive the reward in the `address` field, excluding the `0x`.
```
host: "",
protocol: "ws",
port: 12011,

...

log: {
    level: "debug"
},
wallet: {
    address: "0000111122223333444455556666777788889999",
},
miner: {
    enabled: "yes",
}
```

## Restart Service

To restart the `krypton.service` service, Run:
```bash
sudo systemctl restart krypton.service
```