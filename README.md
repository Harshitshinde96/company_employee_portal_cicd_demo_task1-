# AWS Company Employee Portal CI/CD Demo

This repository demonstrates a simple internal company employee portal built with static HTML and deployed using an AWS Continuous Integration and Continuous Deployment (CI/CD) pipeline.

## Overview

The project showcases a complete CI/CD workflow using AWS Developer Tools (AWS CodeBuild and AWS CodeDeploy). It validates a simple static website, packages it, and automatically deploys it to a web server (Nginx).

## Repository Structure

- `index.html`: The main web page for the Employee Portal.
- `buildspec.yml`: The configuration file for **AWS CodeBuild**. It defines the build phases, including installing an HTML linter (`htmlhint`), validating the HTML file, setting file permissions, and specifying the artifacts for deployment.
- `appspec.yml`: The configuration file for **AWS CodeDeploy**. It manages the deployment of the application to the destination web server (`/usr/share/nginx/html/`), including pre-install and post-install lifecycle hooks.
- `cleanup.sh`: A shell script executed by CodeDeploy before installation to clear the existing web server directory.
- `reload_nginx.sh`: A shell script executed by CodeDeploy after installation to gracefully reload the Nginx service and serve the updated content.

## How It Works

1. **Continuous Integration (CI):** When changes are pushed to the repository, AWS CodeBuild runs the instructions in `buildspec.yml`. It uses `htmlhint` to validate `index.html`. If the validation passes, the necessary files are packaged as build artifacts.
2. **Continuous Deployment (CD):** AWS CodeDeploy takes the artifacts from CodeBuild and follows the instructions in `appspec.yml`.
3. **Deployment Lifecycle:**
   - **BeforeInstall:** CodeDeploy runs `cleanup.sh` to remove old files from `/usr/share/nginx/html/`.
   - **Install:** CodeDeploy copies `index.html` to the target directory.
   - **AfterInstall:** CodeDeploy runs `reload_nginx.sh` to apply changes to the Nginx web server without downtime.

## Setup Instructions

To deploy this project in your AWS environment, you will need:
1. An AWS account with appropriate permissions.
2. An EC2 instance configured with Nginx and the CodeDeploy agent installed.
3. An AWS CodePipeline configured with:
   - A Source stage connected to this repository.
   - A Build stage using AWS CodeBuild (pointing to the `buildspec.yml`).
   - A Deploy stage using AWS CodeDeploy (pointing to the `appspec.yml` and targeting your EC2 instance).

Once the pipeline is set up, any changes pushed to the `main` branch will automatically trigger a build, validation, and deployment to your Nginx server.
