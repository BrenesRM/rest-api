# Testing automation: rest-api

Overview
This project is designed to deploy and test a web application using continuous integration and continuous deployment (CI/CD) pipelines. The workflow integrates Static Application Security Testing (SAST) with CodeQL and Dynamic Application Security Testing (DAST) with ZAP, ensuring that the application is secure during the build and runtime phases.

Table of Contents
Overview
Prerequisites
Setup
GitHub Actions Workflow
SAST Test (CodeQL)
DAST Test (ZAP)
Usage
Contributing
License
Prerequisites
Before you can run this project, ensure that you have the following installed:

Node.js (v20 or later)
npm (comes with Node.js)
Docker (for containerization and image testing)
GitHub Account with secrets set up:
GITHUB_TOKEN: GitHub's default token.
ZAP_TOKEN (Optional for more advanced ZAP setups).
Setup
Clone the Repository:

bash
Copy code
git clone https://github.com/your-repo/project-name.git
cd project-name
Install Dependencies:

bash
Copy code
npm install
Run the Application:

bash
Copy code
npm run dev
Run Tests Locally:

bash
Copy code
npm test
GitHub Actions Workflow
This project includes a CI/CD pipeline that runs automatically on push to the main branch. The workflow integrates both SAST (CodeQL) and DAST (ZAP) security testing to ensure the code is secure.

SAST Test (CodeQL)
The SAST Test performs static analysis of the source code using CodeQL. This test is crucial for identifying vulnerabilities within the code before it is deployed.

CodeQL Workflow:
Trigger: Any code push to the main branch.
Languages: JavaScript (can be extended to other languages as required).
Steps:
Checkout the repository.
Initialize CodeQL for JavaScript analysis.
Run the analysis and report security vulnerabilities.
DAST Test (ZAP)
The DAST Test uses the OWASP ZAP tool to perform dynamic security testing. It scans the running web application to detect runtime vulnerabilities like SQL injection, XSS, etc.

ZAP Workflow:
Trigger: After the SAST test is completed.
Target: The local web server (http://localhost:3000) hosting the web app.
Steps:
Checkout the repository.
Run the application in the background.
Perform a ZAP scan on the running application.
Post results/issues if any vulnerabilities are detected.
Usage
To test the workflow and the application locally:

Run the Application:

bash
Copy code
npm run dev
Run the CI/CD Pipeline: On pushing the code to the main branch, the GitHub Actions workflow will be triggered automatically.

Check the Results: After the workflow is completed, you can review the security vulnerabilities found by CodeQL and ZAP in the GitHub Actions tab of your repository.

Contributing
If you'd like to contribute to this project, feel free to fork the repository and submit a pull request. Make sure to follow best practices for secure coding and testing.

License
This project is licensed under the MIT License. See the LICENSE file for more details.

