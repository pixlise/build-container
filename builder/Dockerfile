ARG GOLANG_VERSION

FROM golang:$GOLANG_VERSION

ARG PROTOC_GEN_GO_VERSION
ARG PROTOC_VERSION
ARG PROTOBUF_CPP_VERSION
ARG ANGULAR_VERSION
ARG NODE_VERSION

MAINTAINER Peter Nemere <peter.nemere@qut.edu.au>

# Generic tools
RUN apt-get -qq update && \
apt-get install -q -y zip unzip jq groff less python3-pip software-properties-common g++ gcc git wget cmake protobuf-compiler unzip make curl libtool automake autoconf && \
rm -rf /var/lib/apt/lists/* && rm -rf /var/cache/apt/archives/*

# Setup for running protobuf compiler
RUN curl -L -o protoc.zip "https://github.com/protocolbuffers/protobuf/releases/download/v${PROTOC_VERSION}/protoc-${PROTOC_VERSION}-linux-x86_64.zip" && \
unzip protoc.zip -d /tmp/protoc3 && \
mv /tmp/protoc3/bin/* /usr/local/bin/ && \
mv /tmp/protoc3/include/* /usr/local/include/ && \
rm -rf /tmp/protoc3


########################################
# NodeJS
########################################

RUN set -uex; \
    apt-get update; \
    apt-get install -y ca-certificates curl gnupg; \
    mkdir -p /etc/apt/keyrings; \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key \
     | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg; \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_${NODE_VERSION}.x nodistro main" \
     > /etc/apt/sources.list.d/nodesource.list; \
    apt-get update; \
    apt-get install nodejs -y; \ 
    node --version && npm --version


########################################
# Angular build tools
########################################

RUN npm install -g @angular/cli@${ANGULAR_VERSION}


########################################
# Deployment tools
########################################

# Setup aws cli so we can deploy
WORKDIR /tmp

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && unzip awscliv2.zip && ./aws/install && rm awscliv2.zip


########################################
# Python libraries
########################################
RUN pip3 install boto3 pyyaml python-gitlab semver jinja2


########################################
# Go Protobuf Compilation
########################################

# Other Go build tools
RUN go install github.com/jstemmer/go-junit-report@v0.9.1

# Go protobuf libs
RUN go install "google.golang.org/protobuf/cmd/protoc-gen-go@v${PROTOC_GEN_GO_VERSION}"


########################################
# C++ Compiler, Protobuf compilation
########################################

# Set up GCC + libraries
# Swiped from: https://github.com/zouzias/docker-boost/blob/master/Dockerfile

WORKDIR /usr

RUN curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v${PROTOBUF_CPP_VERSION}/protobuf-cpp-${PROTOBUF_CPP_VERSION}.zip && \
unzip protobuf-cpp-${PROTOBUF_CPP_VERSION}.zip && rm protobuf-cpp-${PROTOBUF_CPP_VERSION}.zip && \
cd /usr/protobuf-${PROTOBUF_CPP_VERSION} && \
./configure && \
make && \
make install && \
ldconfig && \
rm -rf /usr/protobuf-${PROTOBUF_CPP_VERSION}


########################################
# Kubernetes tools
########################################

RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && \
chmod +x ./kubectl && \
mv ./kubectl /usr/local/bin/kubectl

WORKDIR /build
