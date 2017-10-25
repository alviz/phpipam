# PHPIPAM

docker-compose file

```yaml
version: "3"

services: 
  ipam:
    image: alviz/phpipam:latest
    container_name: ipam
    depends_on:
     - mysql
    restart: always
    ports:
     - "10000:80"
  mysql:
    image: mysql:latest
    container_name: mysql
    restart: always
    ports:
     - "10001:3306"
    environment:
     - MYSQL_ROOT_PASSWORD=<root_password>
    volumes:
     - "/home/phpipam/mysql:/var/lib/mysql"
```
To start from the same folder as docker-compose.yml
```
docker-compose up -d
```
For automatic database provision working need to GRANT access for phpipam user before DB creat via web interface.
```
docker exec -it mysql bin/bash
mysql -u root --password=<MYSQL_ROOT_PASSWORD> -e "GRANT ALL on phpipam.* to 'phpipam'@'%' identified by '<phpipam_pass>';"
exit
```
