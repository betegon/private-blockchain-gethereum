# Private blockchain with geth
This is a quick introduction to create private blockchain networks over Ethereum using geth.

## Requisites ##

* Linux-like Operative System. Tested particularily in Ubuntu 16.04.03 LTE
*

## 0.1 Create a home blockchain folder: ##
```console
foo@bar:~$ mkdir ethereum_private_network
```
## 0.2 Export variable: ##
```console
foo@bar:~$ export ETHEREUM_HOME='/home/foo/Desktop/ethereum_private_network' 
```
###0.2.1 Check: ### 
```console
foo@bar:~$ $ETHEREUM_HOME 
bash: /home/foo/Desktop/ethereum_private_network: Is a directory
foo@bar:~$ printenv | grep ETHEREUM_HOME
ETHEREUM_HOME=/home/foo/Desktop/ethereum_private_network
```
This way, you will have to export the variable every time you start your computer. 
In order to save it, you should save these commands as lines in your `~/.bashrc` file:
```vim
 export ETHEREUM_HOME='/home/foo/Desktop/ethereum_private_network'
```


[genesis.json](genesis.json)


1. create genesis file (chainId: 15, for future references the network id will be 15).

2. Initialize first node: 
$ geth --datadir "$ETHEREUM_HOMEº/node1" init "$ETHEREUM_HOME/genesis.json" # this will create a node in the path given .../node1

3. Start interactive geth  javascript console (so powerful for sending transactions, starting the miner...): 
$ sudo geth --datadir "$ETHEREUM_HOME/node1" console 2>console.log # we want to save all logs in console.log
> eth.blockNumber   # check we are in block 0 (only the genesis block has been created).

4. once in the console type: > admin. and press "tab" (2 times) to see all options you have
5. We can see our node info: > admin.nodeInfo # shows the ip, ports and so on. we will need to use other different port for other nodes (obviously). It also outputs enode: ... this defines our node. to identify it we will use the last 4 hexadecimal numbers before 939f@62.42.(ip)
6. another important for creating accounts,list them, list wallets...: > personal. (press tab 2 times) and shows up all options
7. We will create our first account:
> personal.newAccount()
8. see if the account is well created: 
> personal.listAcounts 

10. Initialize second node: 
$ geth --datadir "$ETHEREUM_HOME/node2" init "$ETHEREUM_HOME/genesis.json" # this will create a node in the path given .../node2. SEE THAT WE use the same genesis block!



11. Start interactive geth  javascript console second node 
$ sudo geth --datadir "$ETHEREUM_HOME/node2" --port 30304 --nodiscover --networkid 1234 console 2>console.log # different port from 1st node. also nodiscover for avoiding the node to attach to other neighbor blockchain (this is little paranoid, but just in case). and network id.



 geth --datadir path/to/custom/data/folder --networkid 15
