# Migration from 1.1-Cezanne to 2.0-Monet

## Description

There are two slightly different migrations path depending on the Pastel software you are upgrading.
* `Simple Node`
* `Walletnode`
* `Supernode`

### `Simple Node` this can be of the following use cases:
* Just Pastel Network node because you want to strengthen the Pastel Network
* SuperNode Cold Node - the Node that controls remote Supernodes in the COLD/HOT setup. It keeps the funds on the collateral transactions of those Supernodes in its wallet

`Simple Node` setup consists of the single Pastel Network application:
* `pasteld`

### `Walletnode` is the setup that allows you to interact with the Storage and Machine Learning capabilities of Pastel Network

`Walletnode` setup consists of the 3 Pastel Network applications:
* `pasteld` service
* `walletnode` service
* `rq-service` service

### `Supernode` is the setup you are running to provide services to Pastel Network users

`Supernode` setup consists of the 5 Pastel Network applications:
* `pasteld` service
* `walletnode` service
* `rq-service` service
* `hermes` service
* `dd-service` service

## `pastelup` utility

`pastelup` is the tool that allows to simplify a lot of the operation related to installation. updating, and running Pastel Network applications

Download new `pastelup` tool (steps shown for Ubuntu and MacOS)
```shell
cd ~
rm pastelup-linux-amd64
wget https://download.pastel.network/latest-release/pastelup/pastelup-linux-amd64
chmod +x pastelup-linux-amd64
```


## Migrations

### Upgrade Ubuntu from 20.04 to 22.04

> NOTE: This is Optional, but HIGHLY recommended!
> NOTE: This is mostly important for `Supernode` upgrades
> If not upgrading OS, still do packages update/upgrade

Update packages:
```shell
sudo apt update
sudo apt upgrade
sudo reboot now
```

Upgrading OS
```shell
sudo apt install pdate-manager-core
do-release-upgrade -f DistUpgradeViewNonInteractive
sudo reboot now
```

> For bulk upgrades it is possible to use ansible playbook
>```ansible
>...
>```


### Simple Node migration

Login to your host that has one of the `Simple Node` setups (for example Cold Node in the COLD/HOT setup).

Get new `pastelup`, if not yet:
```shell
cd ~
rm pastelup-linux-amd64
wget https://download.pastel.network/latest-release/pastelup/pastelup-linux-amd64
chmod +x pastelup-linux-amd64
```

Perform upgrade
```shell
~/pastelup-linux-amd64 update node -f
```

Start node back
```shell
~/pastelup-linux-amd64 start node
```


### Walletnode migration

Login to your host that has one of the `Walletnode Node` setups.

Get new `pastelup`, if not yet:
```shell
cd ~
rm pastelup-linux-amd64
wget https://download.pastel.network/latest-release/pastelup/pastelup-linux-amd64
chmod +x pastelup-linux-amd64
```

Perform upgrade
```shell
~/pastelup-linux-amd64 update walletnode -f
```

Start node back
```shell
~/pastelup-linux-amd64 start walletnode
```


### SuperNode migration

> NOTE: THIS Supernode migration is performed from the Supernode host (HOT node in the COLD/HOT setup)

Get new `pastelup`, if not yet:
```shell
cd ~
rm pastelup-linux-amd64
wget https://download.pastel.network/latest-release/pastelup/pastelup-linux-amd64
chmod +x pastelup-linux-amd64
```

Stop and cleanup
```shell
./pastelup-linux-amd64 stop supernode
./pastelup-linux-amd64 uninstall dd-service --purge
./pastelup-linux-amd64 update remove-service --solution supernode
```

Perform upgrade
```shell
./pastelup-linux-amd64 install dd-service -n mainnet
~/pastelup-linux-amd64 update supernode -f -log-file pastelup.log --skip-system-update=false --skip-dd-packages-update=false --skip-dd-supporting-files-update=false
./pastelup-linux-amd64 update install-service --solution supernode
```

Start Supernode back
```shell
~/pastelup-linux-amd64 start supernode
```

### Advanced migrations

#### Update Supernode remotely - from COLD node in the COLD/HOT setup or just any Simple Node

Get new `pastelup`, if not yet:
```shell
cd ~
rm pastelup-linux-amd64
wget https://download.pastel.network/latest-release/pastelup/pastelup-linux-amd64
chmod +x pastelup-linux-amd64
```

#### Updating single remote SN 

Stop and cleanup
```shell
./pastelup-linux-amd64 stop supernode remote --ssh-ip <> --ssh-user ubuntu --ssh-key <>
./pastelup-linux-amd64 uninstall dd-service remote --purge -f --ssh-ip <> --ssh-user ubuntu --ssh-key <>
./pastelup-linux-amd64 update remove-service remote --solution supernode --ssh-ip <> --ssh-user ubuntu --ssh-key <>
```

Perform upgrade
```shell
./pastelup-linux-amd64 install dd-service remote -n mainnet --ssh-ip <> --ssh-user ubuntu --ssh-key <>
./pastelup-linux-amd64 update supernode remote -f -log-file pastelup.log --skip-system-update=false --skip-dd-packages-update=false --skip-dd-supporting-files-update=false --ssh-ip <> --ssh-user ubuntu --ssh-key <>
./pastelup-linux-amd64 update install-service remote --solution supernode --ssh-ip <> --ssh-user ubuntu --ssh-key <>
```

Start Supernode back
```shell
~/pastelup-linux-amd64 start supernode
```

#### Updating multiple remote SN using inventory file

Stop and cleanup
``` shell
./pastelup-linux-amd64 stop supernode remote --solution supernode --inventory <>
./pastelup-linux-amd64 uninstall dd-service remote --purge -f  --inventory <>
./pastelup-linux-amd64 update remove-service --solution supernode remote --solution supernode --inventory <>
```

Perform upgrade
```shell
./pastelup-linux-amd64 install dd-service remote -n mainnet --inventory <>
./pastelup-linux-amd64 update supernode remote -f -log-file pastelup.log --skip-system-update=false --skip-dd-packages-update=false --skip-dd-supporting-files-update=false --inventory <>
./pastelup-linux-amd64 update install-service remote --solution supernode --inventory <>
```

Start Supernode back
```shell
~/pastelup-linux-amd64 start supernode
```


> NOTE: the `pastelup` uses inventory similar to ansible yaml inventory, with small difference

* ansible inventory format
    ```ansible
    all:
    children:
        group1:
        hosts:
            host01:
            ansible_host: <ip>
            ansible_user: <user>
            host02:
            ansible_host: <ip>
            ansible_user: <user>
    ```
* pastelup inventory
    ```ansible
    group1:
        hosts:
        host01:
            ansible_host: <ip>
            ansible_user: <user>
        host02:
            ansible_host: <ip>
            ansible_user: <user>
    ```
