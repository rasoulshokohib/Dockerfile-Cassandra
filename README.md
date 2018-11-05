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
    
ENV USERNAME mohsen
ENV PASS mortezaie1373

#login
USER -ucassandra -pcassandra -e "CREATE USER ${USERNAME} WITH PASSWORD '${PASS}' SUPERUSER;"

#create KEYSPACE
USER -u${USERNAME} -p${mortezaie1373} -e "CREATE KEYSPACE traderawdata WITH REPLICATION = {'class' : 'SimpleStrategy','replication_factor' : 1};"

#create tables
cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.raw_1d (open_time bigint,close_time bigint,symboltext,closedouble,high double,low double,number_of_trades int,open double,quote_asset_volume double,taker_buy_base_asset_volume double,taker_buy_qoute_asset_volume double,volume double,PRIMARY KEY (open_time, close_time, symbol)) WITH CLUSTERING ORDERBY (close_time ASC, symbol ASC);"

USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.raw_4h (open_time bigint,close_time bigint,symboltext,closedouble,high double,low double,number_of_trades int,open double,quote_asset_volume double,taker_buy_base_asset_volume double,taker_buy_qoute_asset_volume double,volume double,PRIMARY KEY (open_time, close_time, symbol)) WITH CLUSTERING ORDERBY (close_time ASC, symbol ASC);"

USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.raw_1h (open_time bigint,close_time bigint,symboltext,closedouble,high double,low double,number_of_trades int,open double,quote_asset_volume double,taker_buy_base_asset_volume double,taker_buy_qoute_asset_volume double,volume double,PRIMARY KEY (open_time, close_time, symbol)) WITH CLUSTERING ORDERBY (close_time ASC, symbol ASC);"

USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.raw_15m (open_time bigint,close_time bigint,symboltext,closedouble,high double,low double,number_of_trades int,open double,quote_asset_volume double,taker_buy_base_asset_volume double,taker_buy_qoute_asset_volume double,volume double,PRIMARY KEY (open_time, close_time, symbol)) WITH CLUSTERING ORDERBY (close_time ASC, symbol ASC);"



USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.ema_1d (close_time bigint,symbol text,ema map<bigint, double>,high double,low double,PRIMARY KEY (close_time, symbol)) WITH CLUSTERING ORDER BY (symbol ASC);"

USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.ema_4h (close_time bigint,symbol text,ema map<bigint, double>,high double,low double,PRIMARY KEY (close_time, symbol)) WITH CLUSTERING ORDER BY (symbol ASC);"

USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.ema_1h (close_time bigint,symbol text,ema map<bigint, double>,high double,low double,PRIMARY KEY (close_time, symbol)) WITH CLUSTERING ORDER BY (symbol ASC);"

USER -u${USERNAME} -p${mortezaie1373} -e "CREATE TABLE traderawdata.ema_15m (close_time bigint,symbol text,ema map<bigint, double>,high double,low double,PRIMARY KEY (close_time, symbol)) WITH CLUSTERING ORDER BY (symbol ASC);"

EXPOSE 7000 7001 7199 9042 9160

CMD ["cassandra"]


