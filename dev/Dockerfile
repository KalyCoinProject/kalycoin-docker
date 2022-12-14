FROM ubuntu
MAINTAINER pandazwb

#set me to whatever you need to test
ENV BRANCH master

RUN set -ex \
	&& apt-get update \
	&& apt-get install -y -qq --no-install-recommends ca-certificates git wget apt-utils \
	&& apt-get install -y build-essential libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils libgmp3-dev \
	&& apt-get install -y libboost-all-dev \
	&& apt-get install -y software-properties-common \
	&& add-apt-repository -y ppa:bitcoin/bitcoin \
	&& apt-get update \
	&& apt-get install -y libdb4.8-dev libdb4.8++-dev \
	&& apt-get install -y libzmq3-dev \
	&& rm -rf /var/lib/apt/lists/*

# install kalycoin from github
RUN cd /tmp && git clone --recursive https://github.com/kalycoinproject/kalycoin.git \
	&& cd kalycoin \
	&& git checkout $BRANCH \
	&& ./autogen.sh \
	&& ./configure --without-gui --with-gmp=/usr/lib \
	&& make -j4 \
	&& make check \
	&& make install  \
	&& rm -rf /tmp/*

# create data directory
ENV KLC_DATA /data
RUN mkdir $KLC_DATA \
	&& ln -sfn $KLC_DATA /root/.kalycoin \
	VOLUME /data

EXPOSE 3888 3889 13888 13889
CMD ["kalycoind"]
