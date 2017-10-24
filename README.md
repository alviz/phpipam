# PHPIPAM

docker-compose file

```
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
     - MYSQL_ROOT_PASSWORD=P@ssw0rd
    volumes:
     - "/home/phpipam/mysql:/var/lib/mysql"
```
