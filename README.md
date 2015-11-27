# docker-training
Data volume will go to github.

# build data container
docker build -t data-wp .


# start
 docker run --name data-wp -d data-wp tail -f /dev/null
 docker run --name mysql --volumes-from data-wp -e MYSQL\_ROOT\_PASSWORD=mysecretpassword -d mysql
 docker run --name wp --volumes-from data-wp --link mysql:mysql -p 8080:80 -d  maciek01170/docker-training:latest

# backup
 docker run --name data-wp -d data-wp tail -f /dev/null
 docker run --volumes-from data-wp -v $(pwd)/backups:/backups ubuntu tar cfz /backups/root.tgz /var/lib/mysql /var/www/html

# restore 
 docker run --name data-wp -d data-wp tail -f /dev/null
 docker run --volumes-from data-wp -v $(pwd)/backups/:/backups ubuntu bash -c "cd / && tar xvfz /backups/root.tgz"

# sh to the running container
docker exec -it <container_id> /bin/sh

