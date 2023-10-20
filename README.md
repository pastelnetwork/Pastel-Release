# Pastel Chain Releases

---

The repository hosts Pastel Chain releases.

It also contains hassle-free guides and scripts for getting started and managing Pastel Chain clients.

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
* Go Installed
* UPX Installed

_NOTE: Install guides for various client types can be found here - https://github.com/pastelnetwork/pastelup_

_NOTE: Upgrade guides for various client types can be found below._

## 1. WalletNode: Update existing client to latest Pastel Chain version

1. Download `pastelup` to Wallet host
   * https://download.pastel.network/#latest/
2. Run the follwoing command
```shell
./pastelup update walletnode
```
  * The above command will update the following applications:
    * pasteld
    * pastel-cli
    * rq-server
    * walletnode
 
## 2. SuperNode: Update existing client to latest Pastel Chain version

> NOTE: SuperNode Update assumes that SuperNode is established via a COLD/HOT mode set-up where:
>   * `COLD` node has:
>     * pastel node (`pasteld` and `pastel-cli`) set as simple node (not as `masternode`),
>     * wallet with collateral transaction for SN(s) that ran on `HOT` node(s) 
>     * `masternode.conf` for SN(s) that ran on `HOT` node(s)
>   * `HOT` node has:
>     * pastel node (`pasteld` and `pastel-cli`) running as `masternode`
>   * `LOCAL` host - the user's computer used for performing upgrade, this CAN be the `COLD` node  

1. Download `pastelup` to both `COLD` and `HOT` node (and `LOCAL` host if it is different from `COLD` node)
   * https://download.pastel.network/#latest/
2. If `LOCAL` host is NOT `COLD` node, copy `masternode.conf` from `COLD` node to `LOCAL` host
   * `scp cold_host:~/.pastel/masternode.conf .`
3. Open `masternode.conf` and find `MN-NAME`, `COLLATERAL-TX-ID`, `COLLATERAL-TX-IND` and `MN-PRIV-KEY` for node that will be upgraded
    ```json
    {
        "MN-NAME": {
            "mnAddress": "MN-IP-ADDRESS:9933",
            "mnPrivKey": "MN-PRIV-KEY",
            "txid": "COLLATERAL-TX-ID",
            "outIndex": "COLLATERAL-TX-IND",
            "extAddress": "MN-IP-ADDRESS:4444",
            "extCfg": "",
            "extKey": "PASTELID"
        }
    }     
    ```
4. Stop HOT node
   * from `HOT` node itself
     * `pastelup stop node`
   * OR from `LOCAL` host
     * `pastelup stop node remote -ssh-ip <...> --ssh-user <...> --ssh-key <...>`
5. Install Pastel Supernode 
   * from `HOT` node itself
     * `pastelup install supernode -r latest -n mainnet -f`
   * OR from `LOCAL` host
     * `pastelup install supernode -r latest -n mainnet -f -ssh-ip <...> --ssh-user <...> --ssh-key <...>`
6. Initialize supernode, this step is only needed to create new version of `masternode.conf` on `HOT` node and to create new version of PastelID (PastelID in Cezanne has new field) 
   * from `HOT` node itself
     * `pastelup init supernode --name MN-NAME --new --txid=COLLATERAL-TX-ID --ind=COLLATERAL-TX-IND --pkey=MN-PRIV-KEY --passphrase=<passhrase for new PastelID> --skip-collateral-validation`
   * OR from `LOCAL` host
     * `pastelup init supernode remote --name MN-NAME --new --txid=COLLATERAL-TX-ID --ind=COLLATERAL-TX-IND --pkey=MN-PRIV-KEY --passphrase=<passhrase for new PastelID> --skip-collateral-validation -ssh-ip <...> --ssh-user <...> --ssh-key <...>`
   * in both cases `MN-NAME`, `COLLATERAL-TX-ID`, `COLLATERAL-TX-IND` and `MN-PRIV-KEY` are values acquired from masternode.conf in step 2
   * Each of these commands might take a long time and might fail and the end, if it fails, re-run it adding flag `--noReindex`
7. If `init` was successful there will `pasteld` process running on the `HOT` node
   * from `HOT` node
     * ```shell
       $ ps afx | grep pasteld | grep -v grep
         ... /home/user/pastel/pasteld --datadir=/home/user/.pastel --externalip=111.222.333.444 --txindex=1 --masternode --masternodeprivkey=<....>
       ```
8. Update `masternode.conf` on `COLD` node with new `masternode.conf` created on the updated `HOT` node, it will look similar to this
    ```json
    {
        "MN-NAME": {
            "mnAddress": "MN-IP-ADDRESS:9933",
            "mnPrivKey": "MN-PRIV-KEY",
            "txid": "COLLATERAL-TX-ID",
            "outIndex": "COLLATERAL-TX-IND",
            "extAddress": "MN-IP-ADDRESS:4444",
            "extP2P": "MN-IP-ADDRESS:4445",
            "extCfg": "",
            "extKey": "NEW-PASTELID"
        }
    }     
    ```
9. Re-start `pasteld` on `COLD` node (this is required to pickup updates in `masternode.conf`)
   * from `COLD` node
     * `pastelup stop node` OR `./pastel/pastel-cli stop`
     * `./pastel/pasteld --externalip=<external IP of the host> --reindex --txindex=1`
10. Re-enable MN
    * from `COLD` node
         * `./pastel/pastel-cli masternode start-alias MN-NAME`
11. Stop executables on the `HOT` node
    * from `HOT` node itself
      * `pastelup stop supernode`
    * OR from `LOCAL` host
      * `pastelup stop supernode remote -ssh-ip <...> --ssh-user <...> --ssh-key <...>`
12. Set supernode's executables as systemd services
    * from `HOT` node itself
      * `pastelup update install-service --solution supernode`
13. Start supernode
    * from `HOT` node itself
      * `pastelup start supernode`
    * OR from `LOCAL` host
      * `pastelup start supernode -ssh-ip <...> --ssh-user <...> --ssh-key <...>`
