FROM debian:buster-20200607-slim

RUN apt-get update
RUN apt-get install curl -y
RUN apt-get install -y build-essential

RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN apt-get install -y nodejs

#RUN apt-get install python -y
RUN apt-get install bluetooth bluez libbluetooth-dev libudev-dev -y
#RUN apt-get install make gcc g++ -y

WORKDIR /usr/src/app

COPY package.json .
RUN npm i --production

COPY ./*.js ./

CMD [ "node", "index.js" ]