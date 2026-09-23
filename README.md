# Secure Docker CI/CD Pipeline for Flask REST API

A hands-on DevSecOps project demonstrating containerisation, security scanning, CI/CD automation, container image publishing, and automated deployment to AWS EC2.

> The base Flask REST API originates from an upstream open-source project.  
> My contribution focuses on Docker, Docker Compose, container security scanning, CI/CD automation, Docker Hub integration, and AWS EC2 deployment.

## Architecture

```text
Developer Push
      ↓
GitHub Repository
      ↓
GitHub Actions
      ↓
Docker Image Build
      ↓
Trivy Vulnerability Scan
      ↓
Docker Hub
      ↓
Automated SSH Deployment
      ↓
AWS EC2
      ↓
Docker Compose
   ↙          ↘
Flask API    PostgreSQL
```

## Technologies Used

- Docker
- Docker Compose
- Git & GitHub
- GitHub Actions
- Trivy
- Docker Hub
- AWS EC2
- Linux
- Flask
- PostgreSQL

## DevSecOps Workflow

1. Code is pushed to GitHub.
2. GitHub Actions automatically starts the CI pipeline.
3. A Docker image is built from the Dockerfile.
4. Trivy scans the Docker image for HIGH and CRITICAL vulnerabilities.
5. The pipeline fails if the configured vulnerability security gate is violated.
6. Successful Docker images are tagged using the Git commit SHA.
7. The image is pushed automatically to Docker Hub.
8. The deployment job connects securely to AWS EC2 using SSH.
9. The EC2 deployment updates the Docker image tag to the latest Git commit SHA.
10. Docker Compose pulls the new application image.
11. Docker Compose recreates the application container using the new image.
12. PostgreSQL runs as a separate container with persistent storage.

## Docker Architecture

The application consists of:

- Flask REST API container
- PostgreSQL database container
- Docker bridge networking
- Persistent PostgreSQL named volume
- Docker health checks
- Restart policies
- Environment-based configuration
- Docker Compose orchestration

The API and PostgreSQL containers communicate through the internal Docker network.

The PostgreSQL database port is not exposed publicly.

## Security Features

- Trivy container vulnerability scanning
- HIGH and CRITICAL vulnerability security gate
- GitHub Actions Secrets for sensitive credentials
- Docker Hub Personal Access Token for registry authentication
- SSH key authentication for AWS EC2 deployment
- Database port kept private
- `.env` file excluded from Git
- Git commit SHA used for Docker image version traceability

## CI/CD Pipeline

GitHub Actions performs the following automated workflow:

```text
Build → Scan → Tag → Push → Deploy
```

### CI

The Continuous Integration stage performs:

```text
Checkout Source Code
        ↓
Build Docker Image
        ↓
Trivy Security Scan
        ↓
Tag Image with Git SHA
        ↓
Push Image to Docker Hub
```

### CD

The Continuous Deployment stage performs:

```text
GitHub Actions
       ↓
SSH to AWS EC2
       ↓
Update IMAGE_TAG
       ↓
Docker Compose Pull
       ↓
Docker Compose Up
       ↓
New Application Version Running
```

## Docker Image Versioning

Each successful CI build is tagged using the Git commit SHA.

Example:

```text
srinadh164/store-api:<git-commit-sha>
```

This provides traceability between:

```text
Git Commit
    ↓
Docker Image
    ↓
Deployment
```

## PostgreSQL Persistence

PostgreSQL uses a Docker named volume:

```text
store-postgres-data
```

This allows database data to persist even if the PostgreSQL container is recreated.

A normal:

```bash
docker compose down
```

removes the containers and Compose network but keeps the named volume.

## Environment Configuration

Application configuration is provided using environment variables.

Sensitive values are stored in `.env` or GitHub Actions Secrets instead of being hardcoded directly in the Dockerfile or workflow.

The `.env` file is excluded from Git using `.gitignore`.

## Deployment

The application is deployed to an AWS EC2 Ubuntu server.

Production Docker images are pulled from Docker Hub rather than rebuilt directly on the EC2 deployment server.

The production deployment uses:

```bash
docker compose -f compose.prod.yaml pull
docker compose -f compose.prod.yaml up -d
```

The CI/CD pipeline executes the deployment automatically after a successful Docker build, Trivy security scan, and Docker Hub push.

## Health Checks

PostgreSQL uses a Docker health check based on:

```bash
pg_isready
```

The API service waits for PostgreSQL to become healthy before starting.

This avoids application startup failures caused by the database container running but not yet being ready to accept connections.

## Restart Policy

The containers use:

```yaml
restart: unless-stopped
```

This allows Docker to restart containers automatically after unexpected failures while respecting intentional manual stops.

## Project Origin

The base Flask REST API originates from the upstream open-source project.

The application code itself is not presented as my original work.

My contribution to this project focuses on the DevSecOps lifecycle, including:

- Docker containerisation
- Docker Compose configuration
- PostgreSQL container deployment
- Docker networking
- Persistent storage
- Health checks
- Environment variable management
- Trivy vulnerability scanning
- GitHub Actions CI/CD
- Docker Hub integration
- Git SHA image versioning
- AWS EC2 deployment
- SSH-based continuous deployment
- CI/CD troubleshooting

## What I Learned

This project provided practical experience with:

- Writing and troubleshooting Dockerfiles
- Building and running Docker images and containers
- Docker networking
- Docker volumes and persistent storage
- Docker Compose
- Environment variables and secret handling
- Docker health checks
- Restart policies
- Container vulnerability scanning with Trivy
- GitHub Actions workflow development
- Docker Hub image publishing
- Container image versioning
- AWS EC2 deployment
- SSH authentication
- CI/CD automation
- Troubleshooting failed builds and deployments

## Author

**Srinadh**

Aspiring Cloud / DevSecOps Engineer
