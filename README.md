# Dockerfile-Cassandra

FROM ubuntu:16.04

#install openjdk-8-jre
RUN apt-get update && \
    apt-get upgrade -y && \
    apt install -y openjdk-8-jre && \
    java -version
    
#cassandra.sources.list 
RUN echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | tee -a /etc/apt/sources.list.d/cassandra.sources.list
    
#Cassandra repository keys   
RUN curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -  && \
    apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA && \
    apt-get update    
    
#install cassandra    
RUN apt-get install -y cassandra    
    
#configuring authentication  
RUN sed -i 's/authenticator: AllowAllAuthenticator/authenticator: PasswordAuthenticator/g' /etc/cassandra/cassandra.yaml && \
    service cassandra restart && \
    service cassandra start
