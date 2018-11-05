# Dockerfile-Cassandra

FROM ubuntu:16.04

#install openjdk-8-jre
RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends apt-utils && apt-get install -y curl && apt install -y openjdk-8-jre

#cassandra.sources.list
RUN echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | tee -a /etc/apt/sources.list.d/cassandra.sources.lis \ 

#Cassandra repository keys
  && curl https://www.apache.org/dist/cassandra/KEYS | apt-key add -  && apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA &&  apt-get update
  
#install cassandra    
RUN apt-get install -y cassandra

#Cassandra Authentication
RUN  echo "authenticator: PasswordAuthenticator" >> /etc/cassandra/cassandra.yaml

#reset cassandra
RUN /etc/init.d/cassandra stop
RUN /etc/init.d/cassandra start


#login
USER cassandra -e "CREATE USER mohsen WITH PASSWORD 'mortezaie1373' SUPERUSER;"
    
