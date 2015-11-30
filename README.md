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

# Orchestrating...
## Install crane
bash -c "`curl -sL https://raw.githubusercontent.com/michaelsauter/crane/master/download.sh`" && sudo mv crane /usr/local/bin/crane

## Configuration
Create the following crane.yaml file in a new directory:
``̀
containers:
   wp:
      image: maciek01170/docker-training:wordpress
      run:
         volumes-from: ["data-wp"]
         link:
            - mysql-wp:mysql
         publish: ["8080:80"]
         detach: true

   mysql-wp:
      image: mysql
      run:
         volumes-from: ["data-wp"]
         detach: true
         env: ["MYSQL_ROOT_PASSWORD=mysecretpassword"]

   data-wp:
      image: maciek01170/docker-training:data
      run:
         detach: true
         cmd: "tail -f /dev/null"

groups:
   default: ['data-wp', 'mysql-wp', 'wp']
   data_db: ['data-wp', 'mysql-wp']
   web: ['wp']
`̀̀ `

### Using
crane lift
crane status 
... etc... 


# Nginx

