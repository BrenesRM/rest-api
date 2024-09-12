# Testing automation: rest-api

Overview
This updated YAML configuration defines a GitHub Actions workflow named deploy, which implements two types of security testing: SAST (Static Application Security Testing) using CodeQL and DAST (Dynamic Application Security Testing) using ZAP.

Detailed Breakdown:
on: push
The workflow is triggered whenever there is a push to the main branch:

yaml
Copy code
on:
  push:
    branches: ["main"]
Purpose: Ensures that the workflow runs automatically when code is pushed to main.
jobs: build (SAST Testing with CodeQL)

1. Permissions:
yaml
Copy code
permissions:
  security-events: write
  packages: read
  actions: read
  contents: read
security-events: write: Grants the workflow permission to write security-related information (e.g., storing results).
packages, actions, contents: read: Grants read access to packages, actions, and repository contents.

2. Steps:
yaml
Copy code
steps:
  - uses: actions/checkout@v3
    name: Checkout code
Checkout code: This pulls the repository's latest code to ensure the subsequent steps work with the current codebase.
yaml
Copy code
  - name: Initialize CodeQL
    uses: github/codeql-action/init@v3
    with:
      languages: 'javascript'
Initialize CodeQL: This step initializes the CodeQL analysis tool, specifying that it should analyze JavaScript code.
yaml
Copy code
  - name: Perform CodeQL Analysis
    uses: github/codeql-action/analyze@v3
Perform CodeQL Analysis: This runs the actual static analysis using CodeQL. It searches for vulnerabilities or security issues in the codebase.
jobs: dast (DAST Testing with ZAP)
1. Permissions:
yaml
Copy code
permissions:
  issues: write
  #continue-on-error: true
issues: write: Allows the workflow to create issues in the repository if the ZAP test detects any security vulnerabilities.
(Optional) continue-on-error: Can be enabled if you want the workflow to continue despite failures.
2. Steps:
yaml
Copy code
steps:
  - uses: actions/checkout@v3
    name: Checkout code
Checkout code: This step retrieves the current version of the repository's code.
yaml
Copy code
  - name: run api
    run: |
      npm install
      npm run dev&
Run API: Installs the required npm packages and runs the application in the background using npm run dev&, preparing it for the DAST scan.
yaml
Copy code
  - name: ZAP Scan
    uses: zaproxy/action-api-scan@v0.7.0
    with:
      token: ${{ secrets.GITHUB_TOKEN }}
      target: 'http://localhost:3000/search?q=123'
ZAP Scan: Runs an API scan using ZAP (Zed Attack Proxy) to detect potential security vulnerabilities. The scan targets http://localhost:3000/search?q=123, which could be part of the application being tested.

Incorporating the Snyk and SonarCloud integration:

markdown
Copy code
# Project Name

This project is a RESTful API built using Docker, with a CI/CD pipeline managed through GitHub Actions. The workflow includes building, testing, security scans, and load testing to ensure the application is stable, secure, and scalable.

## Table of Contents

- [Project Setup](#project-setup)
- [CI/CD Pipeline](#cicd-pipeline)
  - [Static Application Security Testing (SAST)](#static-application-security-testing-sast)
  - [Dynamic Application Security Testing (DAST)](#dynamic-application-security-testing-dast)
  - [Vulnerability Scanning with Snyk](#vulnerability-scanning-with-snyk)
  - [Code Scanning with SonarCloud](#code-scanning-with-sonarcloud)
  - [Load Testing](#load-testing)
- [Dependencies](#dependencies)
- [Contributing](#contributing)
- [License](#license)

## Image after the test:

![GitHub Security](https://github.com/BrenesRM/rest-api/blob/main/sonarcloud.png)

![GitHub Security](https://github.com/BrenesRM/rest-api/blob/main/snyk.png)

![GitHub Security](https://github.com/user-attachments/assets/98aa1d3a-cc32-46b7-8536-be5ee040e34c)

## Project Setup

### Prerequisites

- Docker installed
- Node.js installed (v20)
- npm installed

### Install Dependencies

To install the project dependencies, run:

```bash
npm install
Run the Project
To run the application locally using Docker:

bash
Copy code
docker-compose up
Alternatively, to run without Docker:

bash
Copy code
npm run dev
CI/CD Pipeline
This project uses GitHub Actions for continuous integration and deployment. The pipeline includes the following jobs:

Static Application Security Testing (SAST)
The SAST testing is done using CodeQL via GitHub Actions. This ensures that the code is statically analyzed for security vulnerabilities before deployment.

The CodeQL workflow performs the following actions:

Initializes the CodeQL engine for javascript.
Runs a static analysis to detect vulnerabilities and security issues in the source code.
Dynamic Application Security Testing (DAST)
The DAST test is performed using OWASP ZAP. It scans the running application to identify potential vulnerabilities that can only be detected while the application is live.

Runs the application using npm run dev.
Scans the local instance at http://localhost:3000 using ZAP to detect potential security flaws.
Vulnerability Scanning with Snyk
The Snyk integration checks for security vulnerabilities in the Docker image during the build process.

The steps in the workflow are:

Login to Docker Hub: The GitHub Action logs in to Docker Hub with stored credentials.
Build & Push Docker Image: The Docker image is built and pushed to the Docker registry.
Snyk Vulnerability Scan: Snyk scans the Docker image to identify potential security vulnerabilities. The scan is run using the Snyk GitHub Action:
yaml
Copy code
- name: Run Snyk to check for vulnerabilities
  uses: snyk/actions/docker@master
  env:
    SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }} 
  with:
    image: ${{ env.DOCKER_REGISTRY }}/${{ env.IMAGE_NAME }}:latest
If any vulnerabilities are found, the workflow can be configured to either fail the build or continue with a warning.

Code Scanning with SonarCloud
SonarCloud is integrated into the pipeline to perform code quality checks and provide static analysis of the codebase. This helps ensure that code adheres to best practices and detects potential bugs and security vulnerabilities.

The SonarCloud integration includes:

SonarCloud Scan: The scan analyzes the codebase using SonarCloud's static code analysis engine.
Check SonarCloud Status: If any issues are detected during the scan, the workflow can be configured to stop the process or continue based on the severity of the findings.
yaml
Copy code
- name: SonarCloud Scan
  uses: sonarsource/sonarcloud-github-action@master
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  with:
    args: >
      -Dsonar.organization=${{ secrets.SONAR_ORGANIZATION }}
      -Dsonar.projectKey=${{ secrets.SONAR_PROJECT_KEY }}
Load Testing
Load testing is performed using Artillery, a modern, powerful, and easy-to-use load testing toolkit. The workflow includes:

Run API: The application is run inside a Docker container.
Artillery Load Test: A load test is performed on the running instance of the application using predefined test cases.
bash
Copy code
artillery run loadTest/basic.yaml
If the load test fails, the workflow will stop and log the failure.

Dependencies
Node.js (v20)
Docker
npm: for managing Node.js dependencies.
Artillery: for load testing.
OWASP ZAP: for dynamic application security testing.
Snyk: for vulnerability scanning.
SonarCloud: for code quality and security checks.
Contributing
We welcome contributions to this project. Please fork the repository and submit a pull request for any changes you'd like to make.

Steps to Contribute
Fork the project repository.
Create a new feature branch.
Make your changes.
Submit a pull request for review.
License
This project is licensed under the MIT License. See the LICENSE file for more details.

markdown
Copy code

### Key Sections:
- **Snyk and SonarCloud** integrations are described under the **CI/CD Pipeline** section.
- **Static (SAST)** and **Dynamic (DAST)** tests are outlined, giving clear instructions on how the workflow operates.
- The **Project Setup** section provides instructions for getting the project up and running.

This structure gives contributors and users a clear understanding of how the project works, along with instructions for reproducing the same setup locally.
