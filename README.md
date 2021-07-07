# Database Container
This is database container running MySQL 5.7, intended to use for local
development environment.

## Preparation
Copy `.env.dist` to `.env`.
Change variables as you need.

* DOCKER_VOLUME_DIR: Path to the directory where your database data will be
  stored on the host machine so that it will not be deleted after the container
  is deleted. The default is local-db directory at the root of this repo.
* CONTAINER_NAME: The name used to refer this container from other container.
* NETWORK_NAME: Other containers accessing this container should be on the
  network of the same name.

## Controlling Database Container
### Create docker network
If you have not created a docker network with the name specified in .env file,
create it with the command below. Example uses `local-net` as its name.  
If you already have it, skip to "Create & Start Container" section.

```
docker network create --driver bridge local-net
```

To see if it is created successfully, execute below.
```
docker network ls
```

If you want to remove the network, this is the command to do it.
```
docker network rm local-net
```

### Create & Start Container
Execute the command below in the directory where docker-compose.yml file resides.
This command creates and runs a container based on the configuration in this yml file.
```
docker-compose up -d
```

### Stop Container
Stopping container does NOT delete the container but shuts down.
```
docker-compose stop
```

### Start Container
Stopped container can be started again by start command.
```
docker-compose start
```

### Delete Container
Deleting container. This command will preserve the database content.
```
docker-compose down
```

To delete the database content also, add `-v` option to delete it together.
```
docker-compose down -v
```


## Connecting to MySQL on the Container
### Administrative User
* username: root
* password: root

### Connect from Host Machine
Below is the command to login to mysql on the container from the host machine.  
The access to the port 53306 of the host machine is forwarded to the port 3306 of the database container.

```
mysql -h 127.0.0.1 -P 53306 -u root -proot
```

### Connect from Other Container
Below is the command to log-in to mysql on the container from the other container.  
The host name of the database container is as specified in .env file.
```
mysql -h local-db -u root -proot
```


## Creating Database
Login to MySQL by either way of above.  
Create DB user and database for your project.

Below is example SQL to
* create a user named as `my_user` with password `my_pass`,
* allow this user to read everything,
* create database named `my_database`,
* and allow this user to do everything on this database.
```sql
CREATE USER 'my_user'@'%' IDENTIFIED BY 'my_pass';
GRANT USAGE ON *.* TO 'my_user'@'%';
CREATE DATABASE IF NOT EXISTS `my_database`;
GRANT ALL PRIVILEGES ON `my_database`.* TO 'my_user'@'%';
```


## Backup Data for Development
Example of taking backup of only `blog_posts` table, only data, from local machine.

Example - backup:
```
$ mysqldump -h 127.0.0.1 -P 53306 -u root -proot -t my_database blog_posts > blog_posts_data_only_20180527.sql
```

Example - restore:
```
mysql -h 127.0.0.1 -P 53306 -u root -proot my_database < blog_posts_data_only_20180527.sql
```
