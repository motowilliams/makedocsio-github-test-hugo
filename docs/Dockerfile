FROM alpine:latest


ENV TZ=US/Mountain
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apk update && \
    apk add --update \
    bash \
    ca-certificates \
    curl \
    docker-cli \
    git \
    libc6-compat \
    libstdc++  \
    make \
    tar \
    unzip \
    zip

# Hugo install
ENV HUGO_VERSION="0.88.1"
RUN curl -sL https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_Linux-64bit.tar.gz --output /tmp/hugo.tar.gz && \
    tar -xf /tmp/hugo.tar.gz -C /usr/local/bin && \
    rm -rf /tmp/*
