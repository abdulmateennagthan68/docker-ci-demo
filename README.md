# Documentation
This repository consist of readme file which have the details tasks.
Task 1: Setup GitHub Repository and Basic CI Workflow
Summary:
Created a GitHub repository and set up a basic CI workflow.

Description:

Created a GitHub repository.
Set up a basic GitHub Actions workflow (ci.yml) to run on push and pull request to the main branch.
Include steps to check the repository, set up Node.js, and run a simple script.
Steps:
Create a GitHub repository:
Go to GitHub and create a new repository.
Name your repository and provide a description (optional).
Choose between public or private visibility.
Click Create Repository.
Set up a basic GitHub Actions workflow (ci.yml):
In the newly created repository, create a .github/workflows/ci.yml file.
Add the following content to ci.yml:
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'

    - name: Run a simple script
      run: echo "Hello, World!"
3 Commit the new file:
Commit the .github/workflows/ci.yml file to the repository.

Task 2: Build and Test Automation
Summary:
Enhance the CI workflow to include build and test steps.

Description:

Enhance the CI workflow to install dependencies (npm install) and run tests (npm test).
Ensure the workflow runs on every push and pull request to the main branch.
Steps:

Edit the .github/workflows/ci.yml file:
Add steps to install dependencies and run tests:
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'

    - name: Install dependencies
      run: npm install

    - name: Run tests
      run: npm test
Task 3: Dockerize the Application
Summary:
Create a Dockerfile to containerize the application.

Description:

Create a Dockerfile to containerize the application.
Build and run the Docker image locally to ensure it works correctly.
Add a GitHub Actions step to build the Docker image.
Steps:

Create a Dockerfile:
In the root of your repository, create a Dockerfile.
Add the following content to the Dockerfile:
dockerfile
Copy code
# Use an official Node.js runtime as a parent image
FROM node:14

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the port the app runs on
EXPOSE 3000

# Define the command to run the app
CMD ["node", "app.js"]
Build and run the Docker image locally:
Open your terminal and navigate to your project directory.
Build the Docker image:
bash
Copy code
docker build -t my-app.
Run the Docker image:
bash
Copy code
docker run -p 3000:3000 my-app
Ensure the application is running correctly by visiting http://localhost:3000.
Add a GitHub Actions step to build the Docker image:
Edit the .github/workflows/ci.yml file:
Add steps to set up Docker Buildx, log in to Docker Hub, and build and push the Docker image:
yaml
Copy code
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2
      with:
        node-version: '14'

    - name: Install dependencies
      run: npm install

    - name: Run tests
      run: npm test

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1

    - name: Log in to Docker Hub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push Docker image
      uses: docker/build-push-action@v2
      with:
        context: .
        push: true
        tags: ${{ secrets.DOCKER_USERNAME }}/my-app:latest
Commit the changes.
Add DOCKER_USERNAME and DOCKER_PASSWORD as secrets in your GitHub repository settings.
Commit the changes.
Add DOCKER_USERNAME and DOCKER_PASSWORD as secrets in your GitHub repository settings.

Task 4
Task 4: Deploy to Kubernetes (Minikube)
Summary:
Deploy the Dockerized application to a local Kubernetes cluster using Minikube.

Description:

Install Minikube and start a local Kubernetes cluster.
Write Kubernetes deployment and service YAML files.
Deploy the application to Minikube and verify the deployment.
Steps:

Install Minikube and start a local Kubernetes cluster:
Follow the instructions on the Minikube installation guide for your operating system.
Start Minikube:
bash
Copy code
minikube start
Write Kubernetes deployment and service YAML files:

Create a deployment.yaml file:
yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: <your-dockerhub-username>/my-app:latest
        ports:
        - containerPort: 3000
Create a service.yaml file:
yaml
Copy code
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
      nodePort: 30036
Deploy the application to Minikube and verify the deployment:

Apply the deployment and service YAML files:
bash
Copy code
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
Verify the deployment:
bash
Copy code
kubectl get pods
kubectl get services

Best Practices for Writing and Maintaining GitHub Actions Workflows
Use secrets for sensitive data:

Store sensitive information such as API keys, passwords, and tokens in GitHub Secrets.
Modularize workflows:

Use reusable workflows and action templates to keep your workflows DRY (Don't Repeat Yourself).
Limit workflow runs:

Use the paths and branches filters to limit when workflows run, reducing unnecessary builds and tests.
Use matrix builds:

Use matrix builds to test across multiple environments and configurations efficiently.
Leverage caching:

Use caching to speed up workflow execution, especially for dependencies and build artifacts.
Monitor and debug workflows:

Use the GitHub Actions logs to monitor workflow runs and debug issues. Add continue-on-error: true for optional steps to ensure critical steps are not skipped.
Common Troubleshooting Tips
Check logs:

Review the detailed logs provided by GitHub Actions to identify the root cause of failures.
Validate YAML syntax:

Ensure your YAML syntax is correct using a YAML linter or online validator.
Verify permissions:

Ensure that your repository and GitHub Actions have the necessary permissions to perform the required tasks.
Test locally:

Test scripts and commands locally to verify their behavior before integrating them into your workflow.
Consult documentation:

Refer to the [GitHub Actions documentation](https://docs.github.com)
