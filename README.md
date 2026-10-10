# Jenkins CI/CD Pipeline with GitHub, Docker, and Docker Hub

## Project Overview

This project demonstrates a basic CI/CD workflow using GitHub, Jenkins,
Docker, and Docker Hub. A code push to GitHub triggers Jenkins through a
webhook. Jenkins checks out the code, builds a Docker image, publishes
the image to Docker Hub, and deploys the application in a Docker
container on an Ubuntu server.

> **Before publishing:** Update the author details and links below.
> Never commit passwords, access tokens, SSH private keys, or other
> secrets.

## Architecture

``` text
Developer
   | git push
   v
GitHub Repository
   | GitHub webhook
   v
Jenkins Pipeline (Ubuntu)
   | Checkout -> Build/Test -> Docker Build
   | Docker Push
   | Deploy and Verify
   v
Docker Container (NGINX)
   Host port 8081 -> Container port 80
```

## Technologies Used

-   Git and GitHub --- source code management
-   Jenkins --- CI/CD pipeline automation
-   Docker --- image building and container runtime
-   Docker Hub --- image registry
-   Ubuntu Linux --- host operating system
-   NGINX --- web server for the sample application

## Features

-   GitHub webhook triggers the Jenkins job after a code push.
-   Jenkins automates pipeline stages.
-   Docker images are built from a Dockerfile.
-   Jenkins pushes images to Docker Hub using Jenkins-managed
    credentials.
-   The application runs in a container and is exposed on host port
    `8081`.
-   Deployment is verified with Docker commands, `curl`, and a browser.

## Prerequisites

-   Ubuntu server with network access
-   GitHub repository containing the application and a `Dockerfile`
-   Jenkins installed and running
-   Docker installed and running
-   Jenkins user permitted to run Docker commands
-   Docker Hub account and repository
-   Docker Hub access token stored in Jenkins Credentials
-   If using AWS EC2, a suitable Security Group rule for TCP port `8081`
    from trusted sources

## Example Dockerfile

This example assumes the repository root contains `index.html`.

``` dockerfile
FROM nginx:stable-alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

## Run the Application Manually

From the directory containing the Dockerfile and `index.html`:

``` bash
docker build -t jenkins-cicd-app:latest .
docker run -d --name jenkins-cicd-container -p 8081:80 jenkins-cicd-app:latest
```

If the container name is already in use, remove the old container before
recreating it:

``` bash
docker rm -f jenkins-cicd-container
```

Verify the container and application:

``` bash
docker ps
curl -I http://localhost:8081
docker logs jenkins-cicd-container
```

Open the application in a browser at:

``` text
http://YOUR_SERVER_PUBLIC_IP:8081
```

Replace `YOUR_SERVER_PUBLIC_IP` with your server's public IP. Limit
access to port `8081` to trusted IP addresses where possible.

## GitHub Webhook Setup

1.  Open the GitHub repository.
2.  Go to **Settings → Webhooks → Add webhook**.
3.  Enter the reachable Jenkins webhook endpoint used by your Jenkins
    GitHub integration, commonly
    `http://YOUR_JENKINS_HOST:8080/github-webhook/`.
4.  Select JSON as the content type and enable push events.
5.  Save the webhook and check its delivery status in GitHub.
6.  Confirm the Jenkins job is configured to trigger on GitHub push
    events.

For real deployments, use a secure, reachable Jenkins URL with HTTPS and
suitable access controls. Avoid exposing Jenkins directly to the public
internet without protection.

## Jenkins Pipeline Stages

Adapt these stages to the actual application and repository:

1.  **Checkout** --- retrieve source code from the configured GitHub
    branch.
2.  **Build** --- build the application, if required.
3.  **Test** --- run actual automated tests, if configured.
4.  **Docker Build** --- build the image from the Dockerfile.
5.  **Docker Push** --- authenticate with Jenkins Credentials and
    publish the image.
6.  **Deploy** --- replace or update the running container.
7.  **Verify** --- confirm the container is running and the application
    responds.

### Docker Hub credentials in Jenkins

Create a Jenkins credential of type **Username with password**:

-   **Username:** your Docker Hub username
-   **Password:** your Docker Hub access token
-   **Credential ID:** `dockerhub-creds` (or the ID configured in your
    pipeline)

Use Jenkins credential binding instead of hardcoding tokens in the
pipeline. Ensure the pipeline uses the correct Docker Hub username and
image name.

## Useful Troubleshooting Commands

  Purpose                      Command
  ---------------------------- --------------------------------------
  Jenkins service status       `sudo systemctl status jenkins`
  Docker service status        `sudo systemctl status docker`
  Running containers           `docker ps`
  All containers               `docker ps -a`
  Docker images                `docker images`
  Container logs               `docker logs jenkins-cicd-container`
  Test local HTTP response     `curl -I http://localhost:8081`
  Test Jenkins Docker access   `sudo -u jenkins docker ps`
  Check current Git branch     `git branch --show-current`
  Check Git changes            `git status`

When a pipeline fails, open the Jenkins build's **Console Output** and
identify the failing stage before changing configuration.

## Security Notes

-   Never commit Docker Hub tokens, passwords, SSH private keys, or
    other secrets.
-   Store credentials in Jenkins Credentials and restrict who can use
    them.
-   Avoid unnecessary privileged access for Jenkins and containers.
-   Limit inbound access to Jenkins and application ports.
-   Restrict AWS Security Group rules to required source IP ranges.
-   Prefer immutable image tags, such as a Git commit SHA or Jenkins
    build number, for traceability.
-   Add real tests, health checks, rollback procedures, monitoring, and
    logging before production use.

## What I Learned

-   Connecting GitHub and Jenkins with a webhook
-   Configuring and troubleshooting Jenkins pipeline stages
-   Building Docker images from a Dockerfile
-   Running and inspecting Docker containers
-   Managing Docker Hub credentials through Jenkins
-   Publishing Docker images to a registry
-   Verifying deployments with `docker ps`, `docker logs`, and `curl`

## Future Improvements

-   Add real build and automated test commands.
-   Tag images with the Jenkins build number or Git commit SHA.
-   Add container health checks and rollback.
-   Add code quality and vulnerability scanning.
-   Add monitoring and centralized logging.
-   Use Ansible for server configuration.
-   Explore deployment to AWS services or Kubernetes.

## Author

**Your Name**

-   GitHub: `https://github.com/a-ashvini`
-   Project repository:
    `https://github.com/a-ashvini/jenkins-cicd-project`
