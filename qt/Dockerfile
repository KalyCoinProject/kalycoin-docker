FROM ubuntu
MAINTAINER pandazwb

RUN set -ex \
    && apt-get update \
    && apt-get install -y -qq --no-install-recommends ca-certificates curl wget apt-utils jq \
    && apt-get install -y libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools protobuf-compiler qrencode

# install kalycoin binaries
RUN set -ex \
    && echo `curl -s https://api.github.com/repos/kalycoinproject/kalycoin/releases/latest | jq -r ".assets[] | select(.name | test(\"x86_64-linux-gnu.tar.gz\")) | .browser_download_url"` > /tmp/kalycoin_url \
    && KLC_URL=`cat /tmp/kalycoin_url` \
    && KLC_DIST=$(basename $KLC_URL) \
    && wget -O $KLC_DIST $KLC_URL \
	&& tar -xzvf $KLC_DIST -C /usr/local --strip-components=1 \
	&& rm /tmp/kalycoin* \
	&& rm -rf /var/lib/apt/lists/*

# create data directory
ENV KLC_DATA /data
RUN mkdir $KLC_DATA \
	&& ln -sfn $KLC_DATA /root/.kalycoin \
VOLUME /data

EXPOSE 3888 3889 13888 13889
CMD ["kalycoin-qt"]
