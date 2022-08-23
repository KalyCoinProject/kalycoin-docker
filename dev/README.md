# Quickstart

This Dockerfile trace the latest dev version of kalycoin.

## Get docker image

You might take either way:

### Pull a image from Public Docker hub

```
$ docker pull kalycoin/kalycoin:dev
```

### Or, build kalycoin image with provided Dockerfile

This is recommended since it ensures build the latest dev version.

```
$docker build --rm -t kalycoin/kalycoin:dev .
```

## Prepare data path and kalycoin.conf

In order to use user-defined config file, as well as save block chain data, -v option for docker is recommended.

First chose a path to save kalycoin block chain data:

```
sudo rm -rf /data/kalycoin-data
sudo mkdir -p /data/kalycoin-data
sudo chmod a+w /data/kalycoin-data
```

Create your config file, refer to the example [kalycoin.conf]!(https://github.com/kalycoinproject/kalycoin/blob/1a926b980f03e97322c7dd787835bec1730f35d2/contrib/debian/examples/kalycoin.conf). Note rpcuser and rpcpassword to required for later `kalycoin-cli` usage for docker, so it is better to set those two options. Then please create the file ${PWD}/kalycoin.conf with content:

```
rpcuser=kalycoin
rpcpassword=kalycointest
```
## Launch kalycoind

To launch kalycoin node:

```
## to launch kalycoind
$ docker run -d --rm --name kalycoin_node -v ${PWD}/kalycoin.conf:/root/.kalycoin/kalycoin.conf -v /data/kalycoin-data/:/root/.kalycoin/ kalycoin/kalycoin:dev kalycoind

## check docker processed
$ docker ps

## to stop kalycoind
$ docker run -i --network container:kalycoin_node -v ${PWD}/kalycoin.conf:/root/.kalycoin/kalycoin.conf -v /data/kalycoin-data/:/root/.kalycoin/ kalycoin/kalycoin:dev kalycoin-cli stop
```

`${PWD}/kalycoin.conf` will be used, and blockchain data saved under /data/kalycoin-data/

## Interact with `kalycoind` using `kalycoin-cli`

Use following docker command to interact with your kalycoin node with `kalycoin-cli`:

```
$ docker run -i --network container:kalycoin_node -v ${PWD}/kalycoin.conf:/root/.kalycoin/kalycoin.conf -v /data/kalycoin-data/:/root/.kalycoin/ kalycoin/kalycoin:dev kalycoin-cli getblockchaininfo
```

For more kalycoin-cli commands, use:

```
$ docker run -i --network container:kalycoin_node -v ${PWD}/kalycoin.conf:/root/.kalycoin/kalycoin.conf -v /data/kalycoin-data/:/root/.kalycoin/ kalycoin/kalycoin:dev kalycoin-cli help
```

