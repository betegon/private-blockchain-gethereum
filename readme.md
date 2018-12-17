# Private blockchain with geth
This is a quick introduction to create private blockchain networks over Ethereum using geth.

## Requisites ##

* **Linux-like Operative System**: Tested particularily in Ubuntu 16.04.03 LTE
* **Geth**:  Tested in version 1.8.1-stable-1e67410e but is should work with all compatible versions. 

## 0.1 Create a home blockchain folder: ##
```console
foo@bar:~$ mkdir ethereum_private_network
```
## 0.2 Export variable: ##
```console
foo@bar:~$ export ETHEREUM_HOME='/home/foo/ethereum_private_network' 
```
### 0.2.1 Check: ### 
```console
foo@bar:~$ $ETHEREUM_HOME 
bash: /home/foo/Desktop/ethereum_private_network: Is a directory
foo@bar:~$ printenv | grep ETHEREUM_HOME
ETHEREUM_HOME=/home/foo/ethereum_private_network
```
This way, you will have to export the variable every time you start your computer. 
In order to save it, you should save these commands as lines in your `~/.bashrc` file:
```vim
 export ETHEREUM_HOME='/home/foo/ethereum_private_network'
```


## 1. Create genesis file  ##
A genesis block is the first block of a block chain. Modern versions of Bitcoin number it as block 0, though very early versions counted it as block 1. The genesis block is almost always hardcoded into the software of the applications that utilize its block chain.

[genesis.json](genesis.json)
```json
{
    "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "difficulty": "100",
    "gasLimit": "2100000",
    "alloc": {
        "7df9a875a174b3bc565e6424a0050ebc1b2d1d82": { "balance": "300000" },
        "f41c74c9ae680c1aa78f42e5647a62f353b7bdde": { "balance": "400000" }
    }
}
```
*  `"config"`: The blockchain configuration.
*  `"chainId"`: Protection of the replay attack(an unauthorized user acting as the original sender). For example, if an action is validated by matching certain value that depends on the chain id, attackers cannot easily get the same value with a different id. Basically, **for future references, the network id will be 15**.
*  `"homesteadBlock"`: Homestead is the second major release of Ethereum(the first release is Frontier). The value 0 means that you are using this release.
*  `"epi155Block"`: epi stands for Ethereum Improvement Proposal, where developers propose ideas on how to improve Ethereum and contribute to this project.
*  `"epi158Block"`:  Your chain won't be hard-forking for these changes, so leave as 0. 
* `"difficulty"`: Mining difficulty. Set this value low so you don’t have to wait too long for mining blocks.
* `"gasLimit"`: The limit of gas cost per block. Set this value high to avoid being limited when testing.
* `"alloc"`:  Pre-funded address, the first parameter of each is the address. Need to be a 40 digits hex string(160 bit, one hex digit is 4 bit). Note that this does not create an account.



 ## 2. Initialize first node: ## 
Command below  will create a node in the path given .../node1
```console
foo@bar:~$ geth --datadir "$ETHEREUM_HOME/node1" init "$ETHEREUM_HOME/genesis.json"
INFO [12-17|09:49:49] Maximum peer count                       ETH=25 LES=0 total=25
INFO [12-17|09:49:49] Allocated cache and file handles         database=/home/betegon/Desktop/ethereum_private_network/node1/geth/chaindata cache=16 handles=16
INFO [12-17|09:49:49] Persisted trie from memory database      nodes=3 size=503.00B time=10.15µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [12-17|09:49:49] Successfully wrote genesis state         database=chaindata                                                           hash=4189da…65f92f
INFO [12-17|09:49:49] Allocated cache and file handles         database=/home/betegon/Desktop/ethereum_private_network/node1/geth/lightchaindata cache=16 handles=16
INFO [12-17|09:49:49] Persisted trie from memory database      nodes=3 size=503.00B time=13.323µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [12-17|09:49:49] Successfully wrote genesis state         database=lightchaindata                                                           hash=4189da…65f92f
```
As it is visible in the logs, everything went fine.

## 3. Start interactive geth javascript console ##
This console is powerful for sending transactions, starting the miner... 
command below let you sabe all logs in console.log.

Once the console is started, we type `eth.blockNumber` to chech we arre currently in block 0 (only the genesis block has been created).

```console
foo@bar:~$ sudo geth --datadir "$ETHEREUM_HOME/node1" console 2>console.log 
[sudo] password for foo: 
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.1-stable-1e67410e/linux-amd64/go1.9.4
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> eth.blockNumber
0
```
## 4. Admin commands: ##
Type `admin.` and press "tab" (2 times) to see all options there are:
```console
> admin. 
admin.addPeer              admin.peers                
admin.clearHistory         admin.propertyIsEnumerable 
admin.constructor          admin.removePeer           
admin.datadir              admin.sleep                
admin.exportChain          admin.sleepBlocks          
admin.getDatadir           admin.startRPC             
admin.getNodeInfo          admin.startWS              
admin.getPeers             admin.stopRPC              
admin.hasOwnProperty       admin.stopWS               
admin.importChain          admin.toLocaleString       
admin.isPrototypeOf        admin.toString             
admin.nodeInfo             admin.valueOf 
```
## 5. See your node info: ##
Show IP, ports and so on. It is needed to use other different port for other nodes (obviously). 
```console
> admin.nodeInfo 
{
  enode: "enode://54d2f58265b1197f6232593e6c530c7bd914ba5e9516e0f98e7078c9b74697eee8afe45dcad2fcd18d38d8a4b129bf7903ff50f5a0f1c6a2898e7a4a27b02e95@[::]:30303",
  id: "54d2f58265b1197f6232593e6c530c7bd914ba5e9516e0f98e7078c9b74697eee8afe45dcad2fcd18d38d8a4b129bf7903ff50f5a0f1c6a2898e7a4a27b02e95",
  ip: "::",
  listenAddr: "[::]:30303",
  name: "Geth/v1.8.1-stable-1e67410e/linux-amd64/go1.9.4",
  ports: {
    discovery: 30303,
    listener: 30303
  },
  protocols: {
    eth: {
      config: {
        chainId: 15,
        eip150Hash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        eip155Block: 0,
        eip158Block: 0,
        homesteadBlock: 0
      },
      difficulty: 100,
      genesis: "0x4189da591f00bb72ff5942b44ba613f25002903e356687b4bf504ab3ba65f92f",
      head: "0x4189da591f00bb72ff5942b44ba613f25002903e356687b4bf504ab3ba65f92f",
      network: 1
    }
  }
}
```

## 6. Personal commands ##
`personal.` is another important command for creating accounts,list them, list wallets...
```console
> personal. (press tab 2 times) and shows up all options
personal._requestManager personal.listWallets     
personal.constructor     personal.lockAccount     
personal.deriveAccount   personal.newAccount      
personal.ecRecover       personal.openWallet      
personal.getListAccounts personal.sendTransaction 
personal.getListWallets  personal.sign            
personal.importRawKey    personal.signTransaction 
personal.listAccounts    personal.unlockAccount   
```

## 7. Create your first account: ##
```console
> personal.newAccount()
Passphrase: 
Repeat passphrase: 
"0x88c19e733f006d1659b360d9d135c9dcb56126b0"
```
## 8. see if the account is well created: ## 
```console
> personal.listAcounts 
["0x88c19e733f006d1659b360d9d135c9dcb56126b0"]
```
## 9. Initialize second node: ##
You can use other terminal if you want, so you can have both open.
NOTE THAT YOU HAVE TO SPECIFY THE SAME [genesis.json](genesis.json).
```console 
foo@bar:~$ geth --datadir "$ETHEREUM_HOME/node2" init "$ETHEREUM_HOME/genesis.json" 
INFO [12-17|10:19:13] Maximum peer count                       ETH=25 LES=0 total=25
INFO [12-17|10:19:13] Allocated cache and file handles         database=/home/betegon/Desktop/ethereum_private_network/node2/geth/chaindata cache=16 handles=16
INFO [12-17|10:19:13] Persisted trie from memory database      nodes=3 size=503.00B time=9.521µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [12-17|10:19:13] Successfully wrote genesis state         database=chaindata                                                           hash=4189da…65f92f
INFO [12-17|10:19:13] Allocated cache and file handles         database=/home/betegon/Desktop/ethereum_private_network/node2/geth/lightchaindata cache=16 handles=16
INFO [12-17|10:19:13] Persisted trie from memory database      nodes=3 size=503.00B time=11.288µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [12-17|10:19:13] Successfully wrote genesis state         database=lightchaindata                                                           hash=4189da…65f92f
```


## 10. Start console for second node ##
 Different port from 1st node. First node port was `listener: 30303` (step 5). 
 Also `nodiscover` is used for avoiding the node to attach to other neighbor blockchain (this is little paranoid, but just in case). and network id.
```console
foo@bar:~$ sudo geth --datadir "$ETHEREUM_HOME/node2" --port 30304 --nodiscover --networkid 1234 console 2>console.log
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.1-stable-1e67410e/linux-amd64/go1.9.4
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> 
```
 
foo@bar:~$ sudo geth --datadir path/to/custom/data/folder --networkid 15
 
 
 ## REFERENCES
 * Definition of Genesis block: https://en.bitcoin.it/wiki/Genesis_block
 *  Genesis block parameters: https://github.com/ethereum/go-ethereum/blob/feeccdf4ec1084b38dac112ff4f86809efd7c0e5/params/config.go#L71
 
