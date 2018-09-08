# The base OS
FROM ubuntu:18.04

MAINTAINER Imran Shah <imran.shah@valebridgetech.com>

LABEL version=0.1

ARG BUILD_JOBS=4
ENV BUILD_JOBS ${BUILD_JOBS}

RUN echo "deb mirror://mirrors.ubuntu.com/mirrors.txt bionic main restricted universe multiverse" > /etc/apt/sources.list && \
    echo "deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-updates main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-security main restricted universe multiverse" >> /etc/apt/sources.list && \
    echo "deb mirror://mirrors.ubuntu.com/mirrors.txt bionic-backports main restricted universe multiverse" >> /etc/apt/sources.list && \
    DEBIAN_FRONTEND=noninteractive apt-get update

RUN apt-get -q -y install \
      wget \
      git \
      automake \
      autoconf \
      autoconf-archive \
      binutils-dev \
      bison \
      curl \
      flex \
      gcc-6 \
      g++-6 \
      gdb \
      ghostscript \
      git \
      google-perftools \
      graphviz \
      libapr1-dev \
      libboost-all-dev \
      libbz2-dev \
      libcap-dev \
      libcppunit-dev \
      libcurl4-openssl-dev \
      libdouble-conversion-dev \
      libdwarf-dev \
      libevent-dev \
      libfftw3-dev \
      libgflags-dev \
      libgtest-dev \
      libiberty-dev \
      libkrb5-dev \
      libleveldb-dev \
      liblua5.2-dev \
      liblzma-dev \
      libnuma-dev \
      libpcap-dev \
      libsasl2-dev \
      libsnappy-dev \
      libsparsehash-dev \
      libssl-dev \
      libsvn-dev \
      libtool \
      linux-tools-generic \
      make \
      openjdk-8-jdk \
      python-setuptools \
      python-pip \
      scons \
      subversion \
      unzip \
      uuid-dev \
      vim \
      zlib1g-dev \
      libzookeeper-mt-dev \
      liblz4-dev \
      cmake \
      cmake-extras \
      libgoogle-glog-dev \
      numactl \
      libjemalloc-dev \
      libmicrohttpd-dev \
      libzstd-dev \  
      libmstch-dev \
      thrift-compiler \
      libfarmhash-dev \
      libsodium-dev \
      gperf
# Install awscli
RUN cd /opt && \
    wget https://s3.amazonaws.com/aws-cli/awscli-bundle.zip && \
    unzip awscli-bundle.zip && \
    ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws && \
    rm awscli-bundle.zip && rm -rf awscli-bundle


# Set fake git credentials, otherwise git cherry-pick would throw fatal error.
RUN git config --global user.email "you@example.com" && \
    git config --global user.name "Your Name"

RUN apt-get install -q -y libunwind-dev

# Adding Java lib path ld.so searching path configuration
RUN JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 && \
    echo $JAVA_HOME/jre/lib/amd64/ >> /etc/ld.so.conf.d/realpin.conf && \
    echo $JAVA_HOME/jre/lib/amd64/server >> /etc/ld.so.conf.d/realpin.conf && \
    echo $JAVA_HOME/jre/lib/amd64/jamvm >> /etc/ld.so.conf.d/realpin.conf

# download folly
RUN cd /opt && \
    git clone https://github.com/facebook/folly && \
    (cd folly && \
     git reset --hard e9842c1e40d1d3e2a1fac9788e14492055855ea3 && \
     mkdir _build && cd _build && cmake .. && \
     CXXFLAGS='-gdwarf-2 -O3 -fno-omit-frame-pointer' && \
     make -j ${BUILD_JOBS}  && \
     make install && \
     ldconfig) && \
    rm -rf folly

# wangle
RUN cd /opt && \
      git clone https://github.com/facebook/wangle && \
      (cd wangle && git checkout v2018.08.20.00 && \
      mkdir wangle/build && cd wangle/build && cmake .. && \
      make -j ${BUILD_JOBS} && \
      make install && \
      ldconfig) && \
    rm -rf wangle

# download fbthrift
RUN cd /opt && \
    git clone https://github.com/facebook/fbthrift && \
    (cd fbthrift && \
     git checkout v2018.08.20.00 && \
     mkdir _build && cd _build && \
     LDFLAGS="-ljemalloc" \
       CPPFLAGS="-std=c++14 -gdwarf-2 -O3 -fno-omit-frame-pointer" \
     cmake .. && \
     make -j ${BUILD_JOBS} && \
     make install && \
     ldconfig) && \
    rm -rf fbthrift

# aws sdk
RUN cd /opt && \
    git clone https://github.com/aws/aws-sdk-cpp.git && \
    (cd aws-sdk-cpp && \
      git checkout 1.6.4 && \
      mkdir build && cd build && \
      cmake -DCMAKE_BUILD_TYPE=Release -DCUSTOM_MEMORY_MANAGEMENT=0 -DSTATIC_LINKING=1 -DBUILD_ONLY="s3" -DENABLE_TESTING=OFF .. && \
      make -j ${BUILD_JOBS} && \
      make install && \
      rm -rf /usr/local/lib/cmake/aws-cpp-* && \
      rm -rf build && mkdir build && cd build && \
      cmake -DCMAKE_BUILD_TYPE=Release -DCUSTOM_MEMORY_MANAGEMENT=0 -DSTATIC_LINKING=0 -DBUILD_ONLY="s3" -DENABLE_TESTING=OFF .. && \
      cd .. && \
      make -j ${BUILD_JOBS} && \
      make install && \
      ldconfig) && \
    rm -rf aws-sdk-cpp

# rocksdb
RUN cd /opt/ && \
    git clone https://github.com/facebook/rocksdb.git && \
    (cd rocksdb && \
     git checkout v5.14.3 && \
#     sed -i -- 's/std::rindex/rindex/g' ./env/env_hdfs.cc && \
#     sed -i -- '/"util\/string_util.h"/a #include "util\/logging.h"' ./env/env_hdfs.cc && \
#     export CLASSPATH= && \
#     for f in `find /usr/lib/hadoop-hdfs | grep jar`; do export CLASSPATH=$CLASSPATH:$f; done && \
#     for f in `find /usr/lib/hadoop | grep jar`; do export CLASSPATH=$CLASSPATH:$f; done && \
#     for f in `find /usr/lib/hadoop/client | grep jar`; do export CLASSPATH=$CLASSPATH:$f; done && \
     USE_SSE=1 \
#     USE_HDFS=1 \
     JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64 \
     LD_LIBRARY_PATH=$JAVA_HOME/jre/lib/amd64/server:$JAVA_HOME/jre/lib/amd64:/usr/lib/hadoop/lib/native \
       JEMALLOC_LIB=" /usr/lib/x86_64-linux-gnu/libjemalloc.a" \
       JEMALLOC_INCLUDE=" -I /usr/include/" \
       EXTRA_CXXFLAGS="-gdwarf-2 -std=c++14 -O3 -fno-omit-frame-pointer" \
       USE_RTTI=1 \
       make -j ${BUILD_JOBS} static_lib  && \
     make -j ${BUILD_JOBS} install-static && \
     ldconfig) && \
    rm -rf rocksdb

# libbf (bloom filter)
RUN cd /opt && \
    git clone https://github.com/mavam/libbf.git && \
    (cd libbf && \
      git reset --hard ca634ad71fe3dd95f513965be6c1d3d7d1f7d5a2 && \
      ./configure && \
      make -j ${BUILD_JOBS} && \
      make install && \
      ldconfig) && \
    rm -rf libbf

RUN cd /opt && \
    git clone https://github.com/facebookincubator/fizz.git && \
    ( cd fizz && git checkout 32e04c5bdebe98e3bdcc0d11877a03bd6ab91c1d && \
    mkdir _build && cd _build && cmake configure ../fizz/ && make -j ${BUILD_JOBS} && \
    make install ) && rm -Rf fizz

RUN cd /opt && \
    git clone https://github.com/facebook/proxygen.git && \
    ( cd proxygen && git checkout 49e1c2a3261835a4f8defb723584ce2cd69a32bc && \
    cd proxygen && autoreconf -ivf && \ 
       CXXFLAGS="-fno-omit-frame-pointer -g" LIBS="-lfolly -lboost_context -lunwind -levent -ldl -lssl -lcrypto" ./configure --disable-shared  && \
     export AM_LIBTOOLFLAGS='--preserve-dup-deps'; make -j ${BUILD_JOBS} && \
     make install ) && rm -Rf proxygen

RUN cd / && git clone https://github.com/phoenixchinar/rocksplicator.git 
RUN cd /rocksplicator && git submodule init && git submodule update
RUN cd /rocksplicator && git pull
RUN (cd rocksplicator && mkdir build && cd build && cmake .. && \
     make -j ${BUILD_JOBS} counter )
