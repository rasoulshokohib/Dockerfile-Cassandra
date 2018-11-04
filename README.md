# Dockerfile-Cassandra

FROM ubuntu:16.04

#install openjdk-8-jre
RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends apt-utils && apt-get install -y curl && apt install -y openjdk-8-jre \

#cassandra.sources.list & repository keys 
RUN echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | tee -a /etc/apt/sources.list.d/cassandra.sources.list && curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add - && apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA && apt-get update
