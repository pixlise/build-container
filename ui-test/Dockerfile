FROM atlassianlabs/docker-node-jdk-chrome-firefox:2024-03-22

ARG ANGULAR_VERSION
ARG NODE_VERSION

MAINTAINER Peter Nemere <peter.nemere@qut.edu.au>


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
# Angular CLI
########################################

RUN npm install -g @angular/cli@${ANGULAR_VERSION}
