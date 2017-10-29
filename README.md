# PHPIPAM v1.3

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
     - mysql-db:/var/lib/mysql

volumes:
  mysql-db:
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

To run IP pingcheck, autodiscovery and dnslookup use crontab on host station. For example: 
from root: crontab -e
```
#check devices availability every 30 min (for subnets with availability check enabled)
*/30 * * * * docker exec ipam /usr/local/bin/php /var/www/html/functions/scripts/pingCheck.php > /var/log/crontab.log 2>&1
#discovery new devices every hour (for subnets with auto discovery enabled)
* */1 * * * docker exec ipam /usr/local/bin/php /var/www/html/functions/scripts/discoveryCheck.php > /var/log/crontab.log 2>&1
#update device names from DNS every day at 03.00 AM
0 3 * * * docker exec ipam /usr/local/bin/php /var/www/html/functions/scripts/resolveIPaddresses.php > /var/log/crontab.log 2>&1
#backup phpipam db everyday
0 4 * * * docker exec mysql mysqldump -u phpipam --password=<phpipam_pass> phpipam > /home/phpipam/backups/phpipam_backup_$(date +"%Y-%m-%d_%H-%M")
#delete backups older than 20 days
0 5 * * * /usr/bin/find /home/phpipam/backups/ -ctime +20 -exec rm {} \;
~```
