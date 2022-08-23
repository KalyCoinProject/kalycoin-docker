# Quickstart

This is a kalycoin-qt image, launch GUI wallet

## Get docker image

To get the latest image, you might take either way:

### Pull a image from Public Docker hub

```
$ docker pull kalycoin/kalycoin-qt
```

### Or, build kalycoin image with provided Dockerfile

```
$docker build --rm -t kalycoin/kalycoin-qt .
```

For historical versions, please visit [docker hub](https://hub.docker.com/r/kalycoin/kalycoin-qt/)

## Prepare data path & kalycoin.conf

In order to use user-defined config file, as well as save block chain data, -v option for docker is recommended.

First chose a path to save kalycoin block chain data:

```
sudo rm -rf /data/kalycoin-data
sudo mkdir -p /data/kalycoin-data
sudo chmod a+w /data/kalycoin-data
```

Create your config file, refer to the example [kalycoin.conf]!(https://github.com/kalycoinproject/kalycoin/blob/1a926b980f03e97322c7dd787835bec1730f35d2/contrib/debian/examples/kalycoin.conf). Then please create the file ${PWD}/kalycoin.conf with content:

```
rpcuser=kalycoin
rpcpassword=kalycointest
```

User can set their own config file on demands.

## Launch kalycoin-qt

For Linux:

```
$ docker run -it --rm \
             -v /tmp/.X11-unix:/tmp/.X11-unix \
             -v ${PWD}/kalycoin.conf:/root/.kalycoin/kalycoin.conf \
             -v /data/kalycoin-data/:/root/.kalycoin/ \
             -e DISPLAY  kalycoin/kalycoin-qt
```

For Mac:

Please refer to
[https://cntnr.io/running-guis-with-docker-on-mac-os-x-a14df6a76efc](https://cntnr.io/running-guis-with-docker-on-mac-os-x-a14df6a76efc) about how to run gui with docker on mac.

```
## install & launch socat
$ brew install socat
$ socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"

## install & open Xquartz
$ brew install xquartz
$ open -a Xquartz

## then set Xquartz preferences "Security-'Allow connections from network clients'"

## launch kalycoin-qt 
$ docker run -e DISPLAY=<your_ip>:0 -v ${PWD}/kalycoin.conf:/root/.kalycoin/kalycoin.conf -v /data/kalycoin-data/:/root/.kalycoin/ kalycoin/kalycoin-qt

```


`${PWD}/kalycoin.conf` will be used, and blockchain data saved under /data/kalycoin-data/


## exit kalycoin-qt

Exit the gui wallet in normal way.


