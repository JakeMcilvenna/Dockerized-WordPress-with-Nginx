# Dockerized WordPress with Nginx

Dockerized WordPress with Nginx is a development environment that leverages Docker and Nginx to quickly set up and run a WordPress site locally. It provides a hassle-free approach to containerizing WordPress and serving it with the lightweight and efficient Nginx web server.

## Prerequisites
Before using this Dockerized WordPress environment, make sure you have the following software installed on your system:
- Docker: [Installation Guide](https://docs.docker.com/get-docker/)
- Docker Compose: [Installation Guide](https://docs.docker.com/compose/install/)

## Usage
To use Dockerized WordPress with Nginx, follow these steps:

1. Clone the repository and navigate to the project directory.
2. Edit the `.env` file in the root of the project directory.
3. Set the required environment variables in the `.env` file. These variables include:
   - `MYSQL_DATABASE`: The name of the MariaDB database.
   - `MYSQL_USER`: The username for the MariaDB database.
   - `MYSQL_PASSWORD`: The password for the MariaDB database user.
   - `MYSQL_ROOT_PASSWORD`: The root password for the MariaDB database.
   - `WORDPRESS_DB_NAME`: The name of the WordPress database.
   - `WORDPRESS_DB_USER`: The username for the WordPress database.
   - `WORDPRESS_DB_PASSWORD`: The password for the WordPress database user.

   Example `.env` file:
```
MYSQL_DATABASE=mydb
MYSQL_USER=myuser
MYSQL_PASSWORD=mypassword
MYSQL_ROOT_PASSWORD=myrootpassword
WORDPRESS_DB_NAME=wordpressdb
WORDPRESS_DB_USER=wpuser
WORDPRESS_DB_PASSWORD=wppassword
```
Customize the values according to your needs.
4. Open a terminal or command prompt and navigate to the project directory.
5. Run the following command to start the containers:
```
docker-compose up -d
```
This command will create and start the Docker containers based on the provided configuration.
6. Wait for the containers to start. Once they are up and running, you can access your WordPress site by opening a web browser and navigating to http://localhost:8085.

### Configuration
This Docker Compose file consists of three services: `db`, `wordpress`, and `nginx`. Here's a breakdown of each service and its configuration options:

#### db Service
The `db` service uses the `mariadb:10` Docker image to provide a MariaDB database for the WordPress installation.

- `image`: The Docker image used for the `db` service.
- `container_name`: The name of the container for the `db` service.
- `environment`: Environment variables for configuring the MariaDB database. Customize the values of `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD`, and `MYSQL_ROOT_PASSWORD` to suit your needs.

#### wordpress Service
The `wordpress` service uses the `wordpress:php8.1-fpm-alpine` Docker image to run the WordPress PHP code.

- `image`: The Docker image used for the `wordpress` service.
- `container_name`: The name of the container for the `wordpress` service.
- `volumes`: Mounts the `wp_files` directory from the host system to the `/var/www/html` directory inside the container. This allows you to persist data and make changes to the WordPress installation.
- `environment`: Environment variables used by WordPress to connect to the MariaDB database. Customize the values of `WORDPRESS_DB_HOST`, `WORDPRESS_DB_NAME`, `WORDPRESS_DB_USER`, and `WORDPRESS_DB_PASSWORD` to match your database configuration.

#### nginx Service
The `nginx` service uses the `nginx:alpine` Docker image as a lightweight web server that serves the WordPress site.

- `image`: The Docker image used for the `nginx` service.
- `container_name`: The name of the container for the `nginx` service.
- `depends_on`: Specifies that the `nginx` service depends on the `wordpress` service. This ensures that the `wordpress` service starts before the `nginx` service.
- `ports`: Maps port 8085 on the host system to port 80 inside the `nginx` container. You can customize the host port as needed.
- `volumes`: Mounts the `conf.d` and `wp_files` directories from the host system to `/etc/nginx/conf.d` and `/var/www/html` inside the `nginx` container. This allows you to provide custom Nginx configuration and serve the WordPress files.

## Nginx Configuration

### Overview
The provided Nginx configuration file is used within the Docker Compose setup to serve the WordPress site. This configuration file specifies the server settings, file paths, and PHP handling for Nginx. It works in conjunction with the `nginx` service defined in the Docker Compose file.

### Configuration
The Nginx configuration file contains the following settings:

#### Server Settings
- `listen 80;` and `listen [::]:80;`: Specifies that Nginx should listen on port 80 for HTTP connections.
- `server_name localhost;`: Defines the server name for the Nginx server. In this case, it is set to `localhost`.

#### Root Directory
- `root /var/www/html;`: Specifies the root directory for the Nginx server, where the WordPress files are located. By default, it is set to `/var/www/html`.

#### Access Log
- `access_log off;`: Disables the access log for Nginx, so no access logs will be generated.

#### Index File
- `index index.php;`: Sets the index file for Nginx to `index.php`. This means that if a directory is accessed, Nginx will look for an `index.php` file to serve as the default page.

#### Server Tokens
- `server_tokens off;`: Disables the server token response headers, which hide the version number of the server software.

#### Location Block - /
- `location / { ... }`: Handles the requests for the root directory (`/`) of the server.
- `try_files $uri $uri/ /index.php?$args;`: This directive attempts to serve the requested URI as a file, then as a directory, and if both fail, it redirects the request to `index.php` with the original query string (`$args`).

#### Location Block - .php Files
- `location ~ \.php$ { ... }`: Handles requests for PHP files.
- `fastcgi_pass wordpress:9000;`: Forwards PHP requests to the `wordpress` service running on port `9000`.
- `fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;`: Sets the `SCRIPT_FILENAME` parameter for the FastCGI process to the document root directory concatenated with the requested PHP script name.
- `fastcgi_param SCRIPT_NAME $fastcgi_script_name;`: Sets the `SCRIPT_NAME` parameter for the FastCGI process to the requested PHP script name.
- `include fastcgi_params;`: Includes the default FastCGI configuration parameters.
- `fastcgi_index index.php;`: Sets the `fastcgi_index` directive to `index.php`, specifying that if the PHP file is not explicitly mentioned in the request, it should default to `index.php`.

### Usage
To use this Nginx configuration file, follow these steps:
1. Include the Nginx configuration file in your project directory.
2. Update your Docker Compose file to mount the directory containing the Nginx configuration file to `/etc/nginx/conf.d` within the `nginx` container.
- Example Docker Compose volume entry: `- ./conf.d:/etc/nginx/conf.d`
3. Ensure that the `nginx` service in your Docker Compose file references the correct image and container name.
4. Run `docker-compose up -d` to start the containers.
5. Access your WordPress site by opening a web browser and navigating to `http://localhost:8085` (assuming you haven't modified the port mapping).

### Note
Ensure that the Nginx configuration file (`nginx.conf`) is located in the `conf.d` directory relative to the Docker Compose file or adjust the volume mapping accordingly in the Docker Compose file.

Please make sure to review and customise the configuration files according to your specific requirements before using them in a production environment.