# Pastel Chain Releases

---

The repository hosts Pastel Chain releases.

It also contains hassle-free guides and scripts for getting started and managing Pastel Chain clients.

# ❗ The latest release is Monet 2.0.0 ❗️

## Release Files

* All required release files can be found [here](https://download.pastel.network/#latest-release/)
* [pastelup](https://github.com/pastelnetwork/pastelup/releases)
* [paslel-core](https://github.com/pastelnetwork/pastel/releases)
* [goNode](https://github.com/pastelnetwork/gonode/releases)
* [dd-service](https://github.com/pastelnetwork/dd-service/releases)
* [rq-service](https://github.com/pastelnetwork/rqservice/releases)

## Requirements

The following requirements are required to run the node natively.

### Hardware 

The following table shows the recommended hardware requirements for the running node on the mainnet.

| Full Node | WalletNode | SuperNode | 
| ----------- | ----------- | ----------- |
| Memory: 8Gb | Memory: 16Gb | Memory: 16Gb+ | 
| Storage: 50Gb | Storage: 1Tb | Storage: 1Tb+ | 
| Network: 5Gbps+	| Network: 5Gbps+ | Network: 5Gbps+ |

### OS / Software 
* Ubuntu 22.04 Required for running a SuperNode (Note: Full Node and WalletNode clients compatible on MacOS, Windows, and Linux)

_NOTE: Install guides for various client types can be found here - https://github.com/pastelnetwork/pastelup_

_NOTE: Upgrade guides for various client types can be found below._

## 0. Instruction for updating full node client if halted due to fork during Mainnnet Upgrade

```shell
  # navigate to where pasteld binary is located:
  ./pastel-cli-linux-amd64 stop
  mv pasteld-linux-amd64 pasteld-linux-amd64OLD
  mv pastel-cli-linux-amd64 pastel-cli-linux-amd64OLD
  wget https://github.com/pastelnetwork/pastel/releases/download/v2.0.4/pasteld-linux-amd64
  wget https://github.com/pastelnetwork/pastel/releases/download/v2.0.4/pastel-cli-linux-amd64
  sudo chmod +x pasteld-linux-amd64
  sudo chmod +x pastel-cli-linux-amd64
  ./pasteld-linux-amd64 -rescan -reindex -printtoconsole=2 -txindex=1
```


## 1. WalletNode: Update existing client to latest Pastel Chain version

1. Download `pastelup` to Wallet host
   * https://download.pastel.network/#latest/
2. Run the update command
  ```shell
  ./pastelup update walletnode
  ```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
    * rq-server
    * walletnode
3. Start all services
  ```shell
  ./pastelup start walletnode
  ```
 
## 2. SuperNode: Update existing client to latest Pastel Chain version

> NOTE: SuperNode Update assumes that SuperNode is established via a COLD/HOT mode set-up where:
>   * `COLD` node has:
>     * pastel node (`pasteld` and `pastel-cli`) set as simple node (not as `masternode`),
>     * wallet with collateral transaction for SN(s) that ran on `HOT` node(s) 
>     * `masternode.conf` for SN(s) that ran on `HOT` node(s)
>   * `HOT` node has:
>     * pastel node (`pasteld` and `pastel-cli`) running as `masternode`
>   * `LOCAL` host - the user's computer used for performing upgrade, this CAN be the `COLD` node  

### 2.1 Update existing client to latest Pastel Chain version FROM LOCAL Node

1. Download `pastelup` to `LOCAL` node (we assume that node doesn't have pastel application installed)
   * https://download.pastel.network/#latest/
2. Update `COLD` node by running the following command from `LOCAL` node
  ```shell
  ./pastelup update node remote --ssh-ip <IP address of HOT node> --ssh-user <ssh user of HOT node> --ssh-key <path to the key of HOT node user>
  ```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
3. Update `HOT` node by running the following command from `LOCAL` node:
  ```shell
  ./pastelup-darwin-amd64 update supernode remote --ssh-ip <IP address of HOT node> --ssh-user <ssh user of HOT node> --ssh-key <path to the key of HOT node user>
  ```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
    * rq-server
    * hermes
    * supernode
4. Start all services by running the following command from `LOCAL` node:
  1. Start Supernode on `HOT` node:
    ```shell
    ./pastelup start supernode remote --ssh-ip <IP address of HOT node> --ssh-user <ssh user of HOT node> --ssh-key <path to the key of HOT node user>
    ```
  2. _NOTE: In MOST cases you don't need to start pastel application on `COLD` node after upgrade!!!_

### 2.2 Update existing client to latest Pastel Chain version FROM COLD Node

1. Download `pastelup` to `COLD` node
   * https://download.pastel.network/#latest/
2. Update `COLD` itself node by running the following command from `COLD` node
  ```shell
  ./pastelup update node
  ```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
3. Update `HOT` node by running the following command from `COLD` node:
  ```shell
  ./pastelup-darwin-amd64 update supernode remote --ssh-ip <IP address of HOT node> --ssh-user <ssh user of HOT node> --ssh-key <path to the key of HOT node user>
  ```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
    * rq-server
    * hermes
    * supernode
4. Start all services by running the following command from `COLD` node:
  1. Start Supernode on `HOT` node:
    ```shell
    ./pastelup start supernode remote --ssh-ip <IP address of HOT node> --ssh-user <ssh user of HOT node> --ssh-key <path to the key of HOT node user>
    ```
  2. _NOTE: In MOST cases you don't need to start pastel application on `COLD` node after upgrade!!!_

### 2.3 Update existing client to latest Pastel Chain version FROM COLD and HOT Node

1. Download `pastelup` to `COLD` and `HOT` nodes
   * https://download.pastel.network/#latest/
2. Update `COLD` node by running the following command from `COLD` node itself:
```shell
./pastelup update node
```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
3. Update `HOT` node by running the following command from `HOT` node itself:
```shell
./pastelup-darwin-amd64 update supernode
```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
    * rq-server
    * hermes
    * supernode
4. Start all services by running the following command:
  1. Start Supernode on `HOT` node:
    ```shell
    ./pastelup start supernode
    ```
  2. _NOTE: In MOST cases you don't need to start pastel application on `COLD` node after upgrade!!!_
