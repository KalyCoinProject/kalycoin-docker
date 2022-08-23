FROM ubuntu as tools
MAINTAINER pandazwb

RUN apt-get update && \
  apt-get install -y wget unzip && \
  apt-get install -y curl vim-tiny less && \
  rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/mattn/goreman/releases/download/v0.0.10/goreman_linux_amd64.zip && \
  unzip -o -d /usr/local/bin goreman_linux_amd64.zip && \
  rm goreman_linux_amd64.zip

ENV KLC_RELEASE 0.15.2
ENV KLC_RELEASE_TAR kalycoin-${KLC_RELEASE}-x86_64-linux-gnu.tar.gz

RUN wget https://github.com/kalycoinproject/kalycoin/releases/download/mainnet-ignition-v${KLC_RELEASE}/${KLC_RELEASE_TAR} && \
  tar -xf $KLC_RELEASE_TAR -C /usr/local --strip-components=1 --exclude=*-qt --exclude=test_kalycoin --exclude=kalycoin-tx && \
  rm $KLC_RELEASE_TAR

RUN wget https://github.com/ethereum/solidity/releases/download/v0.4.24/solc-static-linux -O /usr/local/bin/solc && \
   chmod 0755 /usr/local/bin/solc

RUN wget -v https://github.com/kalycoinproject/solar/releases/download/0.0.14/solar-linux-amd64 -O /usr/local/bin/solar && chmod 0755 /usr/local/bin/solar 

RUN wget https://github.com/kalycoinproject/kalycoinportal/releases/download/0.0.8/kalycoinportal-linux -O /usr/local/bin/kalycoinportal && chmod 0755 /usr/local/bin/kalycoinportal

# Use multi-stage build makes the image smaller by about 80 MB
FROM jeanblanchard/alpine-glibc

# RUN apt-get update && \
#   apt-get install -y curl vim-tiny less && \
#   rm -rf /var/lib/apt/lists/*

ENV KLC_DATADIR /dapp/.kalycoin
ENV KLC_RPC_USER kalycoin
ENV KLC_RPC_PASS test
ENV KLC_RPC http://$KLC_RPC_USER:$KLC_RPC_PASS@localhost:3889
ENV KLC_NETWORK regtest

VOLUME /dapp

COPY --from=tools /usr/local/bin /usr/local/bin

COPY qcli /usr/local/bin
COPY kalycoind-launch /usr/local/bin
COPY Procfile /Procfile

# 9888: kalycoinportal DApp RPC service
# 9899: kalycoinportal auth service
# 3889: kalycoind RPC
# 3888: peering port
# 13888: peering port (testnet)
EXPOSE 9888 9899 3889 3888 13888

WORKDIR /dapp

# 172.17.0.0/16 is the network submask for docker's bridge network.
# -rpcallowip=172.17.0.0/16 permits RPC access from docker host
CMD mkdir -p $KLC_DATADIR && goreman -f /Procfile start
