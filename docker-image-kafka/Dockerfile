FROM ubuntu
ENV DEBIAN_FRONTEND noninteractive

# Fluent Bit version
ENV FLB_MAJOR 0
ENV FLB_MINOR 11
ENV FLB_PATCH 3
ENV FLB_VERSION 0.11.3
ENV GOPATH /go
ENV GOBIN $GOPATH/bin
ENV PATH $GOBIN:$PATH

MAINTAINER Leah Petersen <leahnpetersen@gmail.com>
USER root

# Install build tools
RUN apt-get -qq update && \
    apt-get install -y -qq curl ca-certificates build-essential cmake iputils-ping dnsutils make bash sudo wget unzip nano vim valgrind golang git  && \
    apt-get install -y -qq --reinstall lsb-base lsb-release && \
    wget -O "/tmp/fluent-bit-$FLB_VERSION-dev.zip" "http://github.com/fluent/fluent-bit/archive/v$FLB_VERSION.zip" && \
    cd /tmp && \
    unzip "fluent-bit-$FLB_VERSION-dev.zip" && \
    cd "fluent-bit-$FLB_VERSION/build/" && \
    cmake -DFLB_DEBUG=On -DFLB_TRACE=On \
          -DCMAKE_INSTALL_PREFIX=/fluent-bit/ -DFLB_JEMALLOC=On ../&& \
    make && make install && \
    cd / && \
    git clone https://github.com/leahnp/fluent-bit-kafka-output-plugin && \
    cd fluent-bit-kafka-output-plugin && \
    make && \
    rm -rf /tmp/* /fluent-bit/include /fluent-bit/lib*

COPY fluent-bit.conf /fluent-bit/etc/
COPY parsers.conf /fluent-bit/etc/

CMD ["/fluent-bit/bin/fluent-bit", "-v", "-e", "/fluent-bit-kafka-output-plugin/out_kafka.so", "-c", "/fluent-bit/etc/fluent-bit.conf"]