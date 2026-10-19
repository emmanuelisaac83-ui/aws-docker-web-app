# Dockerized Web Application Deployment on AWS EC2

## Overview

This hands-on project demonstrates how to package a static website in a Docker image, deploy it on Amazon EC2, publish the image to Docker Hub, and run the published image on a second EC2 instance. Docker Compose is also used to define a web service and a MySQL database service.

## Architecture

- **AWS networking:** VPC, public subnet, internet gateway and security groups.
- **Compute:** Ubuntu EC2 instances for the web deployment and pull test.
- **Containerization:** Docker Engine, Dockerfile and Docker Compose.
- **Web server:** Nginx running in a container.
- **Database service:** MySQL 8.4 with a named persistent volume.
- **Image registry:** Docker Hub.
- **Validation:** Container status, HTTP responses, image publishing and pull-and-run testing on a second EC2 instance.

## Project workflow

1. Configure AWS networking and EC2 access.
2. Install Docker Engine and verify the installation.
3. Create the HTML page and Dockerfile.
4. Build and test the Docker image locally on EC2.
5. Define the web and MySQL services using Docker Compose.
6. Publish the application image to Docker Hub.
7. Launch a second EC2 instance, install Docker and pull the published image.
8. Run the image on the second instance and verify browser access.

## Run the web application locally

**Requirements:** Docker Engine and Docker Compose installed on the host machine. For this project, Docker was installed and used on Ubuntu EC2 instances, accessed remotely through MobaXterm. The commands below can be run on any compatible host with Docker installed.

Build the image:

```bash
docker build -t docker-web-app:v1 .
```

Run the container:

```bash
docker run -d --name docker-web -p 80:80 docker-web-app:v1
```

Test the web server:

```bash
curl http://localhost
docker ps
```

Open `http://localhost` on a machine with the container running, or use the server's reachable public IP when deployed on EC2 and permitted by its security group.

## Run with Docker Compose

Create your local environment file from the example:

```bash
cp .env.example .env
```

Edit `.env` and replace every placeholder with strong, unique credentials. Never commit `.env`.

Start the services:

```bash
docker compose up -d --build
docker compose ps
```

Inspect the logs:

```bash
docker compose logs --tail=100
```

Test the web service:

```bash
curl http://localhost
```

Stop the services:

```bash
docker compose down
```

The named `mysql_data` volume preserves MySQL data when containers are recreated. `docker compose down -v` removes the volume and its stored database data, so use it only when that deletion is intended.

**Note:** The HTML page is a static frontend. The Compose file runs MySQL as a separate service, but the frontend does not currently query the database.

## Docker Hub

Published image: `emmanuelokogun/docker-web-app:v1`

Example commands:

```bash
docker login
docker tag docker-web-app:v1 emmanuelokogun/docker-web-app:v1
docker push emmanuelokogun/docker-web-app:v1
```

On a second server with Docker installed:

```bash
docker pull emmanuelokogun/docker-web-app:v1
docker run -d --name docker-web -p 80:80 emmanuelokogun/docker-web-app:v1
docker ps
curl http://localhost
```

The second server must allow inbound TCP port 80 in its security group for public browser access. Restrict SSH access to trusted source IPs.

## Evidence

The `screenshots/` directory contains selected evidence from the implementation, including AWS networking, EC2, Docker installation, image building, Compose deployment, Docker Hub publishing and the second-server test.

Review screenshots before making the repository public. Remove any passwords, access tokens, private keys or other credentials if present.

## Security considerations

- Never commit `.env`, private keys, AWS credentials or database passwords.
- Restrict SSH access to trusted addresses.
- Expose only the ports required by the application.
- Keep database access private; do not expose MySQL publicly.
- Use least-privilege IAM permissions.
- Review AWS resources and billing after completing the lab.

## Learning outcomes

- AWS VPC and EC2 deployment fundamentals.
- Docker image creation and container lifecycle management.
- Docker Compose service configuration and persistent volumes.
- Container image publishing and pulling from Docker Hub.
- Deployment verification and basic troubleshooting.

## Project status

Practical implementation and selected evidence have been collected. This repository documents the lab; review the configuration, credentials and screenshots before publishing.
