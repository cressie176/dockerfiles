FROM ubuntu:16.04

# Prepare ubuntu
ENV DEBIAN_FRONTEND noninteractive
RUN locale-gen en_GB en_GB.UTF-8
RUN ln -sf /bin/bash /bin/sh

# Install packages
ENV APT_PACKAGES curl git man vim dnsutils tcpdump python2.7 libpq-dev build-essential
RUN apt-get update && \
    apt-get install -y $APT_PACKAGES && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# iconv, required by body-parser needs Python >= v2.5.0 & < 3.0.0.
ENV PYTHON=/usr/bin/python2.7

# Configure standard environment
WORKDIR /root/service

# Configure node
ENV NODE_ENV production

# Install NVM
RUN curl -L https://raw.githubusercontent.com/creationix/nvm/v0.31.1/install.sh | HOME=/root bash

# Invalidate cache if .nvmrc changes
ONBUILD COPY .nvmrc /root/service/.nvmrc-temp

# Make nvm install node specified in .nvmrc
ONBUILD RUN source /root/.nvm/nvm.sh \

  # HACK: Without this, sourcing of nvm.sh would fail. nvm.sh would try to load the version in .nvmrc that is not yet installed
  && mv /root/service/.nvmrc-temp /root/service/.nvmrc \

  # Install nodejs version specified in .nvmrc
  && nvm install \
  && nvm alias default \
  && ln -sf $(which node) /usr/bin/node \
  && ln -sf $(which npm) /usr/bin/npm

# Configure npm
ONBUILD COPY .npmrc /root/.npmrc

# npm-shrinkwrap.json and package.json are used to bust the cache
ONBUILD COPY npm-shrinkwrap.json /root/service/npm-shrinkwrap.json
ONBUILD COPY package.json /root/service/package.json

ONBUILD RUN NODE_ENV=development npm install && npm cache clean

# Copy everything not excluded by .gitignore and .dockerignore
ONBUILD COPY ./ /root/service/


