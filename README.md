# Wordpress-project

## Repository Description

A Docker-based Wordpress and Mysql Database setup with persistent data storage and easy configuration using Docker Compose.

## Table of Content

- [Quickstart](#quickstart)
    - [Prerequisities](#prerequisities)
    - [Run on Docker](#run-on-docker)
- [Usage](#usage)
    - [Wordpress Configuration](#wordpress-configuration)
        - [Image](#image)
        - [Ports](#ports)
        - [Enviroment](#environment)
        - [Volumes](#volumes)
    - [Database Configuration](#database-configuration)
        - [Image](#image-1)
        - [Enviroment](#environment-1)
        - [Volumes](#volumes-1)
    

## Quickstart

In order to quickly get started with the project follow these steps:

### Prerequisities

- [Docker](https://www.docker.com/products/docker-desktop)

1. Clone the repository

2. Navigate to the repository

3. Create a `.env` based on the `.env.example` file
```
cp .env.example .env
```

4. Before starting the containers, you need to configure the database log-in credentials in the `.env` file.

### Run on Docker

Make sure Docker Desktop is running and that you are in the root directory of the project.

1. Start the Wordpress and the Mysql database with:

```
docker compose up -d 
```

3. Check whether both containers is running:

```
docker compose ps
```

4. To view both container logs:

```
docker compose logs wordpress
docker compose logs db
```

Once the server has started successfully, the WordPress page can be accessed by entering [IP-Server]:8080 in the browser's address bar.

>[!NOTE]
>It may take a few minutes for the WordPress page to start.

## Usage

In this section you can read about the project a bit more in detail.


### Wordpress Configuration

The WordPress container can be configured in the `docker-compose.yml` file. The main important configuration options are `image`, `ports`, `environment`, and `volumes`.

#### Image

The image specifies the Docker image that is used to create the WordPress container.

For example:

`image: wordpress:latest`


This tells Docker to use the official WordPress image with the latest tag. The image contains the required software to run WordPress, including the web server and PHP environment.

You can also use a specific WordPress version instead of latest to ensure a consistent environment:

`image: wordpress:7.1-php8.2-apache`

Using a specific version is recommended for production environments to avoid unexpected changes when using `latest`. Available WordPress image versions and tags can be found on the [official WordPress Docker Hub page](https://hub.docker.com/_/wordpress).

#### Ports

The ports configuration defines which ports are exposed from the container to the host system.

For example:
```
ports:
  - "8080:80"
```

The first port (8080) is the port on the host machine, while the second port (80) is the port inside the WordPress container.

This means that requests `http://[SERVER-IP]:8080` are forwarded to port 80 inside the WordPress container.

#### Environment

The environment section is used to provide configuration values to the WordPress container.

For example:
```
environment:
  WORDPRESS_DB_HOST: db
  WORDPRESS_DB_USER: ${WORDPRESS_DB_USER}
  WORDPRESS_DB_PASSWORD: ${WORDPRESS_DB_PASSWORD}
  WORDPRESS_DB_NAME: ${WORDPRESS_DB_NAME}
```

These variables tell WordPress how to connect to the database container.

The values can be stored in the `.env` file instead of being written directly into the docker-compose.yml file. This keeps passwords and other configuration values separate from the Compose configuration.

For example, the `.env` file can contain:
```
WORDPRESS_DB_USER=wordpress
WORDPRESS_DB_PASSWORD=your-secure-password
WORDPRESS_DB_NAME=wordpress
MYSQL_ROOT_PASSWORD=your-root-password
```

#### Volumes

The volumes configuration is used to persist WordPress data outside the container.

For example:
```
volumes:
  - wp_content:/var/www/html/wp-content
```

The WordPress files are stored in the Docker volume `wp-content`. This ensures that the data is not lost when the WordPress container is stopped, restarted, or recreated.

Without a persistent volume, important WordPress files and uploaded content could be lost when the container is removed.

### Database Configuration

The database container is responsible for storing the WordPress database, including posts, pages, users, settings, and other WordPress data.

#### Image

The image specifies which database software and version are used.

For example:

`image: mysql:8.4`

This uses MySQL 8.4 as the database server.

To check which database versions are compatible with WordPress, please refer to the [official WordPress compatibility page](https://make.wordpress.org/hosting/handbook/compatibility/).

#### Environment

The database credentials and database name are configured using environment variables.

For example:
```
environment:
  MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
  MYSQL_DATABASE: ${WORDPRESS_DB_NAME}
  MYSQL_USER: ${WORDPRESS_DB_USER}
  MYSQL_PASSWORD: ${WORDPRESS_DB_PASSWORD}

```
The values are taken from the `.env` file.

For example:
```
WORDPRESS_DB_USER=wordpress
WORDPRESS_DB_PASSWORD=your-secure-password
WORDPRESS_DB_NAME=wordpress
MYSQL_ROOT_PASSWORD=your-root-password
```

`MYSQL_ROOT_PASSWORD` defines the password for the MySQL root user.

`MYSQL_DATABASE` defines the database that will be created for WordPress.

`MYSQL_USER` defines the database user that WordPress uses to connect to MySQL.

`MYSQL_PASSWORD` defines the password for this database user.

#### Volumes

The MySQL database should also use a persistent volume:

```
volumes:
  - db_data:/var/lib/mysql
```

The volume stores the MySQL database files outside the container.

This is important because removing and recreating the database container without a persistent volume can result in the loss of the entire WordPress database.



