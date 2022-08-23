FROM ubuntu
LABEL maintainers "earlz, VoR0220"

WORKDIR /root
RUN set -emx \
    && apt-get update \
    && apt-get install -y -qq --no-install-recommends ca-certificates curl wget apt-utils build-essential git libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils git cmake libboost-all-dev software-properties-common texinfo bison flex libmpc-dev libmpfr-dev libgmp3-dev yasm golang-go\
    && add-apt-repository ppa:bitcoin/bitcoin -y \
    && apt-get update \
    && apt-get install libdb4.8-dev libdb4.8++-dev -y -qq --no-install-recommends


ENV REPO_PREFIX=https://github.com/kalycoinproject
ENV BRANCH=proto-x86
ENV MAKE_SUFFIX=-j2


# get x86-toolchain source
RUN set -ex \
    && git clone $REPO_PREFIX/x86-toolchain \
    && cd x86-toolchain \
    && git checkout $BRANCH




ENV PREFIX=/opt/cross
ENV TARGET=i686-elf
ENV SYSROOT=/opt/x86-compiler/sysroot
ENV PATH=${PATH}:${SYSROOT}/bin:$PREFIX/bin:/root/kalycoin:/root/kalycoin:/root/kalycoin-docker/proto-x86/utils:/root/go/bin

# build the freestanding compiler
RUN set -ex \
    && cd x86-toolchain \
    && mkdir build-binutils \
    && cd build-binutils \
    && ../binutils-2.29/configure --target="$TARGET" --prefix="$PREFIX" --disable-werror \
    && make \
    && make install \
    && cd .. \
    && mkdir build-gcc \
    && cd build-gcc \
    && ../gcc-7.2.0/configure --target="$TARGET" --prefix="$PREFIX" --enable-languages=c,c++ \
    && make all-gcc \
    && make all-target-libgcc \
    && make install-gcc \
    && make install-target-libgcc

#get libc
RUN set -ex \
    && git clone $REPO_PREFIX/FsLibc \
    && cd FsLibc \
    && git checkout $BRANCH

ENV TARGET=i686-kalycoin

#compile KalyCoinOS compiler and supports
RUN set -ex \
    && ls $PREFIX/bin \
    && i686-elf-gcc -v \
    && cd FsLibc \
    && cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_TOOLCHAIN_FILE=cross-toolchain.cmake -DCMAKE_INSTALL_PREFIX=$SYSROOT/usr . \
    && make -C libc \
    && make -C libc install \
    && cd ~/x86-toolchain/crtfiles \
    && make \
    && cd .. \
    && mkdir -p $SYSROOT/usr/lib \
    && mkdir -p $SYSROOT/usr/include \
    && cp crtfiles/*.o $SYSROOT/usr/lib/ \
    && cp -r includes/* $SYSROOT/usr/include/ \
    && rm -rf build-binutils \
    && mkdir build-binutils \
    && cd build-binutils \
    && ../binutils-2.29/configure --target=$TARGET --prefix="$PREFIX" --with-sysroot="$SYSROOT" --disable-werror \
    && make \
    && make install \
    && cd .. \
    && rm -rf build-gcc \
    && mkdir build-gcc \
    && cd build-gcc \
    && ../gcc-7.2.0/configure --target=$TARGET --prefix="$PREFIX" --with-sysroot="$SYSROOT" --enable-languages=c \
    && make all-gcc \
    && make all-target-libgcc \
    && make install-gcc \
    && make install-target-libgcc



# get kalycoin source
RUN set -ex \
    && git clone $REPO_PREFIX/kalycoin kalycoin-compile --recursive \
    && cd kalycoin-compile \
    && git checkout $BRANCH

# build kalycoin
RUN set -ex \
    && cd kalycoin-compile \
    && ./autogen.sh \
    && ./configure --disable-tests --without-gui \
    && make $MAKE_SUFFIX

# build x86lib utility
RUN set -ex \
    && cd kalycoin-compile/src/x86lib \ 
    && make $MAKE_SUFFIX

#install kalycoin tools
RUN set -ex \
    && cd kalycoin-compile \
    && mkdir /root/kalycoin \
    && cp src/kalycoind src/kalycoin-cli src/kalycoin-tx src/x86lib/x86testbench /root/kalycoin

# get libkalycoin
RUN set -ex \
    && git clone $REPO_PREFIX/libkalycoin \
    && cd libkalycoin \
    && git checkout $BRANCH

# build libkalycoin
RUN set -ex \
    && cd libkalycoin \
    && make \
    && make deploy

# get simpleabi (this url should change in the future)
RUN go get -u github.com/kalycoinproject/SimpleABI && go install github.com/kalycoinproject/SimpleABI

# get docker helpers
RUN set -ex \
    && git clone $REPO_PREFIX/kalycoin-docker \
    && cd kalycoin-docker \
    && git checkout $BRANCH

#clean up
RUN set -ex \
    && rm -rf kalycoin-compile \
    && rm -rf x86-toolchain

# create data directory
ENV KLC_DATA /data
RUN mkdir $KLC_DATA \
    && ln -sfn $KLC_DATA /root/.kalycoin \
VOLUME /data

EXPOSE 3888 3889 13888 13889
CMD ["kalycoind", "-regtest", "-logevents"]
