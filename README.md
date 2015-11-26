# docker-training
Data volume will go to github.

# start
 docker run --name data-wp -d data-wp tail -f /dev/null
 docker run --name mysql --volumes-from data-wp -e MYSQL\_ROOT\_PASSWORD=mysecretpassword -d mysql
 docker run --name wp --volumes-from data-wp --link mysql:mysql -p 8080:80 -d  maciek01170/docker-training:latest

# backup
 docker run --name data-wp -d data-wp tail -f /dev/null
 docker run --volumes-from data-wp -v $(pwd):/backup ubuntu tar cfz /backup/root.tgz /

# restore 
 docker run --name data-wp -d data-wp tail -f /dev/null
 docker run --volumes-from data-wp -v $(pwd):/backup ubuntu bash -c "cd / && tar xvfz /backup/root.tgz"

# sh to the running container
docker exec -it <container_id> /bin/sh

