ARG node_version=14.5.0


# Avoid installing of dependencies by stripping version number from package.json
FROM node:${node_version}-slim as package.json.withoutversion

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

COPY package.json .
RUN sed -i '/"version"/d' ./package.json



FROM debian:buster-20200607-slim as dependencies

RUN apt-get update
RUN apt-get install curl -y
RUN apt-get install -y build-essential

RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN apt-get install -y nodejs

RUN apt-get install bluetooth bluez libbluetooth-dev libudev-dev -y

WORKDIR /usr/src/app

COPY --from=package.json.withoutversion /usr/src/app .
RUN npm i --production



FROM node:${node_version}-slim

WORKDIR /usr/src/app

COPY --from=dependencies /usr/src/app .
COPY *.md package.json ./
COPY ./*.js ./

CMD [ "node", "index.js" ]