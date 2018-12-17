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

## 3. Start interactive geth  javascript console ##
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
## 4. once in the console type: ##
> admin. and press "tab" (2 times) to see all options you have
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
Show IP, ports and so on. It is needed to use other different port for other nodes (obviously). It also outputs enode: ... this defines our node. to identify it we will use the last 4 hexadecimal numbers before 939f@62.42.(ip)
> admin.nodeInfo 

## 6. another important command for creating accounts,list them, list wallets...: ##
> personal. (press tab 2 times) and shows up all options

## 7. We will create our first account: ##
> personal.newAccount()
## 8. see if the account is well created: ## 
> personal.listAcounts 

## 9. Initialize second node: ##
$ geth --datadir "$ETHEREUM_HOME/node2" init "$ETHEREUM_HOME/genesis.json" # this will create a node in the path given .../node2. SEE THAT WE use the same genesis block!



## 10. Start interactive geth  javascript console second node ##
$ sudo geth --datadir "$ETHEREUM_HOME/node2" --port 30304 --nodiscover --networkid 1234 console 2>console.log # different port from 1st node. also nodiscover for avoiding the node to attach to other neighbor blockchain (this is little paranoid, but just in case). and network id.
 geth --datadir path/to/custom/data/folder --networkid 15
 
 
 
 ## REFERENCES
 * Definition of Genesis block: https://en.bitcoin.it/wiki/Genesis_block
 *  Genesis block parameters: https://github.com/ethereum/go-ethereum/blob/feeccdf4ec1084b38dac112ff4f86809efd7c0e5/params/config.go#L71
 
