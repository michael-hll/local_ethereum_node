# Setup a Local Ethereum Node (Geth/Prysm)

While studying blockchain knowledge, I want to build a local ethereum node. Initially I thought it's a very easy task, but it costs me nearly 2 weeks to finally set it up in my ubuntu server vm.
That's the reason to share the steps I did and hope it could help others if they want to do so also.


Initially I followed the steps from this: [https://github.com/coderaidershaun/full-node-eth-bsc-docs](https://github.com/coderaidershaun/full-node-eth-bsc-docs/tree/main/ethereum)  (link 1)
After around 10 times trying, I didn't made it. During the setups, when encountered errors, I usually asked help from Claude 3.7, but listen to me, Claude 3.7 is not good at to fix the node setup issues currently. So don't believe all it told to you.

Then googled this document: [https://docs.prylabs.network/docs/install/install-with-script](https://docs.prylabs.network/docs/install/install-with-script) (link 2)
I have to say, I should follow this official document earlier, that's another lesson learned. But the steps in this official document doesn't fully work in my case. I need to change some arguments to make it work. Later you will see.


Ok, time to see what I did!

## Learn some basic knowledge about ethereum and Geth/Prysm, this part is critical, since you need to understand basics of ethereum and geth/prysm on how they work.

## Preparing VM
```
My Laptop: 
 CPU: M3 Max
 Memory: 36 GB
 Disk: 1 TB
```

From this link you can find hardware requirements: [https://geth.ethereum.org/docs/getting-started/hardware-requirements](https://geth.ethereum.org/docs/getting-started/hardware-requirements) (link 3)
Basically you need to have a 16G memory + 2T disk. Apparently my disk is not big enough.
Then I bought a 4T portable ssd, the testings shows it can reach 3100 mb/s. 

Since I don't want to set up a big node in my laptop, then I followed the link 1 installed a fresh ubuntu server and config all settings:
```
-- Ubuntu Server Version: ubuntu-24.04.2-live-server-arm64.iso
-- Memory Size: 20 GB
-- Disk Space: 2 TB (remember to set the hard disk to 2 TB in parallel settings before the installation, and after the ubuntu server installed, make sure the command df -h / shows at least 2 TB size disk, otherwise please ask Claude 3.7 for help how to achieve it) 
-- Config SSHServer (Claude 3.7 could help on this, you can check the ssh server during the setup ubuntu process)
```
## Installed Softwares
SSH to your ubuntu server (VSCode remote server is a good choice):
```
-- Installed all the setups from link 1 except softwares: ethereum, geth and prysm.
-- Follow the steps from link 2, the prysm official document to set up geth and prysm. Just using my below commands to start the geth and prysm:
```

Start Geth:
```
-- locate to your geth folder
-- run starting geth command:
```
```bash
./geth --mainnet --http --http.api eth,net,engine,admin,web3,debug,txpool --ws --ws.port 8546 --ws.api eth,net,web3,txpool,debug --authrpc.jwtsecret=/home/mich/ethereum/jwt.hex --cache=8192 --maxpeers=50
```
Open a new terminal and SSH to your server

Start Prysm (beacon-node):
```
-- locate to your prysm folder
-- run starting prysm command (once your node is synced, remove the checkpoints from the arguments):
```
```bash
./prysm.sh beacon-chain --execution-endpoint=http://localhost:8551 --mainnet --jwt-secret=/home/mich/ethereum/jwt.hex --checkpoint-sync-url=https://sync-mainnet.beaconcha.in --genesis-beacon-api-url=https://sync-mainnet.beaconcha.in --p2p-max-peers=30
```

The following command are useful to check the status:
```
./prysm.sh beacon-chain --version
./geth version
curl http://localhost:3500/eth/v1/node/syncing | jq
./geth attach http://localhost:8545
```
Note: You need to have the knowledge of the output of the above checking status command, if not please learn it first or asking Claude 3.7
During the syncing process, you will see lots of warnings and errors, you need to identify which errors you need to deal with, and which not.
Claude 3.7 is very helpful to help you understand the therory. but the command it suggests please be carefull, follow the steps from the official
document first.
If missing some softwares, ask Claude 3.7, it will told you how to set it up.

Other tips:
```
1. From link 1, you can find it using the systemctl service, which generally it's more convinent when you know details of how geth/prysm works
2. When you want to stop or start the geth/prysm process, make sure the order of start/stop them. It gives me the feeling the order is important after fails lots of trying.
   - Starting: start geth first, wait about 30 seconds, then start prysm
   - Stopping: stop prysm first, then stop prysm
3. Usually it will need about 1 ~ 2 days to fully set it up successfully.
```

Usually you will encounter some issues, hope after some research you can fully sync it successfully!

