FROM ubuntu
MAINTAINER pandazwb

RUN set -ex \
    && apt-get update \
    && apt-get install -y -qq --no-install-recommends ca-certificates curl wget apt-utils jq

# install kalycoin binaries
RUN set -ex \
    && echo `curl -s https://api.github.com/repos/kalycoinproject/kalycoin/releases/latest | jq -r ".assets[] | select(.name | test(\"$(uname -p | sed s/ppc/powerpc/ | sed s/armv7l/arm/).*-linux.*.tar.gz\")) | .browser_download_url"` > /tmp/kalycoin_url \
    && KLC_URL=`cat /tmp/kalycoin_url` \
    && KLC_DIST=$(basename $KLC_URL) \
    && wget -O $KLC_DIST $KLC_URL \
	&& tar -xzvf $KLC_DIST -C /usr/local --strip-components=1 \
	&& rm /tmp/kalycoin*

# create data directory
ENV KLC_DATA /data
RUN mkdir $KLC_DATA \
	&& ln -sfn $KLC_DATA /root/.kalycoin \
VOLUME /data

EXPOSE 3888 3889 13888 13889
CMD ["kalycoind"]
