FROM node as builder

ARG NPM_REGISTRY=https://registry.npmjs.org/
# This is how we can control what version is being published, as 
# Settings does not leverage Releases.
ARG COMMIT_HASH=53feb726f6bfe3404d2cf1f0cfdbf65e6bd54538

ADD https://github.com/github/rally/archive/${COMMIT_HASH}.zip /

RUN apt-get update && \
    apt-get install -y unzip --no-install-recommends && \
    unzip ${COMMIT_HASH}.zip -d /${COMMIT_HASH} && \
    mv ${COMMIT_HASH}/rally-${COMMIT_HASH} /probot && \
    cd probot && \
    npm config set registry ${NPM_REGISTRY} && \
    npm install;

FROM node as probotPrep
ARG NPM_REGISTRY
COPY --from=builder probot/ ./probot
WORKDIR /probot
RUN npm config set registry ${NPM_REGISTRY} && \
    npm install probot

FROM node:slim as app
COPY --from=probotPrep probot/ /probot
VOLUME ["/probot"]
WORKDIR /probot
USER node
ENTRYPOINT  [ "npm", "run", "start" ]
