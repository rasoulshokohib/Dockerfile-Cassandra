# Dockerfile-Cassandra

FROM ubuntu:16.4

#install openjdk-8-jre
RUN apt-get update && \
    apt-get upgrade && \
    apt install openjdk-8-jre && \
    java -version
    
#cassandra.sources.list 
RUN echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | tee -a /etc/apt/sources.list.d/cassandra.sources.list
    
#Cassandra repository keys   
RUN curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -  && \
    apt-key adv --keyserver pool.sks-keyservers.net --recv-key A278B781FE4B2BDA && \
    apt-get update    
    
#install cassandra    
RUN apt-get install cassandra    
    
#configuring authentication  
RUN sed -i 's/authenticator: AllowAllAuthenticator/authenticator: PasswordAuthenticator/g' /etc/cassandra/cassandra.yaml && \
    service cassandra restart && \
    service cassandra start

#login
RUN cqlsh -u cassandra -p cassandra -e "CREATE USER mohsen WITH PASSWORD 'mortezaie1373' SUPERUSER;"

#create KEYSPACE
RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE KEYSPACE traderawdata
  WITH REPLICATION = { 
   'class' : 'SimpleStrategy', 
   'replication_factor' : 1 
  };"

#show KEYSPACE
RUN cqlsh -u mohsen -p mortezaie1373 -e "DESC keyspaces"

#create table
RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.raw_1d (
open_time bigint,
close_time bigint,
symbol text,
close double,
high double,
low double,
number_of_trades int,
open double,
quote_asset_volume double,
taker_buy_base_asset_volume double,
taker_buy_qoute_asset_volume double,
volume double,
PRIMARY KEY (open_time, close_time, symbol)
) WITH CLUSTERING ORDER BY (close_time ASC, symbol ASC);"

RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.raw_4h (
open_time bigint,
close_time bigint,
symbol text,
close double,
high double,
low double,
number_of_trades int,
open double,
quote_asset_volume double,
taker_buy_base_asset_volume double,
taker_buy_qoute_asset_volume double,
volume double,
PRIMARY KEY (open_time, close_time, symbol)
) WITH CLUSTERING ORDER BY (close_time ASC, symbol ASC);"


RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.raw_1h (
open_time bigint,
close_time bigint,
symbol text,
close double,
high double,
low double,
number_of_trades int,
open double,
quote_asset_volume double,
taker_buy_base_asset_volume double,
taker_buy_qoute_asset_volume double,
volume double,
PRIMARY KEY (open_time, close_time, symbol)
) WITH CLUSTERING ORDER BY (close_time ASC, symbol ASC);"


RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.raw_15m (
open_time bigint,
close_time bigint,
symbol text,
close double,
high double,
low double,
number_of_trades int,
open double,
quote_asset_volume double,
taker_buy_base_asset_volume double,
taker_buy_qoute_asset_volume double,
volume double,
PRIMARY KEY (open_time, close_time, symbol)
) WITH CLUSTERING ORDER BY (close_time ASC, symbol ASC);"


RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.ema_1d (
close_time bigint,
symbol text,
ema map<bigint, double>,
high double,
low double,
PRIMARY KEY (close_time, symbol)
) WITH CLUSTERING ORDER BY (symbol ASC);"


RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.ema_4h (
close_time bigint,
symbol text,
ema map<bigint, double>,
high double,
low double,
PRIMARY KEY (close_time, symbol)
) WITH CLUSTERING ORDER BY (symbol ASC);"


RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.ema_1h (
close_time bigint,
symbol text,
ema map<bigint, double>,
high double,
low double,
PRIMARY KEY (close_time, symbol)
) WITH CLUSTERING ORDER BY (symbol ASC);"


RUN cqlsh -u mohsen -p mortezaie1373 -e "CREATE TABLE traderawdata.ema_15m (
close_time bigint,
symbol text,
ema map<bigint, double>,
high double,
low double,
PRIMARY KEY (close_time, symbol)
) WITH CLUSTERING ORDER BY (symbol ASC);"


EXPOSE 7199 7000 7001 9160 9042 22 8012 61621

CMD ["cassandra"]
