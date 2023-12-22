# Apache Cassandra SSL Docker Container

* Create Dockerfile from docker image

docker history cassandra --no-trunc > Dockerfile

* Create docker image from Dockerfile and push to hub.docker.com

 docker login -u adramazany
 docker build -t adramazany/cassandra:4.1.3-ssl .
 docker run -d --name cassandra -p 9042:9042 -e CASSANDRA_LISTEN_ADDRESS=0.0.0.0 adramazany/cassandra:4.1.3-ssl
 docker push

* Create SSL enabled version of apache cassandra 4.1.3 from docker

 docker pull cassandra:latest
 docker tag cassandra:latest adramazany/cassandra:4.1.3-ssl
 docker run -d --name cassandra -p 9042:9042 adramazany/cassandra:4.1.3-ssl
 docker cp cassandra.keystore cassandra:/opt/cassandra/conf/
 docker cp cassandra.truststore cassandra:/opt/cassandra/conf/
 docker exec -it cassandra bash
 cd /etc/cassandra/ ; ls
 sed -i -r '0,/^(\s\senabled:).*/{s//\1 true/}' cassandra.yaml
 sed -r '0,/^(\s\skeystore:.*)/{s// \1/}' cassandra.yaml | sed -r '0,/^(\s\skeystore:).*/{s//\1 \/etc\/cassandra\/cassandra.keystore/}' | sed -r '0,/^(\s)(\s\skeystore:.*)/{s//\2/}' > 1.tmp
 sed -r '0,/^(\s\skeystore_password:.*)/{s// \1/}' 1.tmp | sed -r '0,/^(\s\skeystore_password:).*/{s//\1 Pala_Max007/}' | sed -r '0,/^(\s)(\s\skeystore_password:.*)/{s//\2/}' > 2.tmp
 rm -f cassandra.yaml 1.tmp ; mv 2.tmp cassandra.yaml
 grep -n "\s\senabled:" cassandra.yaml
 grep -n "^\s\skeystore" cassandra.yaml
 exit
 docker restart cassandra
 docker logs -f cassandra

* connect CQLSH to ssl version of cassandra 

python cqlsh.py localhost 9042 -u cassandra -p cassandra --ssl
SELECT * FROM system_schema.tables;