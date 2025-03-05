# Dockerize-LAMP
Dockerize LAMP

## 1. Project Structure
* Create a project folder and navigate into it:

```bash
mkdir lamp_docker && cd lamp_docker
```
Inside this directory, create the following structure:

```bash
mkdir -p lamp_docker/src lamp_docker/mysql lamp_docker/nginx
touch lamp_docker/src/index.php lamp_docker/mysql/init.sql lamp_docker/nginx/default.conf
touch lamp_docker/docker-compose.yml lamp_docker/Dockerfile
```
