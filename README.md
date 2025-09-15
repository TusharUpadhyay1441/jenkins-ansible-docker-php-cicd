## Jenkins CI/CD Pipeline with Ansible and Docker for PHP Project 

# 1. Project Overview 
This project demonstrates a fully automated CI/CD pipeline using Jenkins, Ansible, and 
Docker to deploy a PHP application in a Docker container on a Jenkins slave node. It 
automates Docker installation, Docker image build from GitHub, container deployment, 
and cleanup on failures.

# 2. Environment Setup
The Jenkins Master-Slave architecture allows distributing build workloads. Here, the 
Jenkins master controls builds, and the slave node executes jobs, providing a clean, 
isolated environment. 
Screenshot:

# 3. Ansible Playbook Creation 
The install_docker.yml playbook automates Docker Engine installation on the slave 
node by updating package caches, installing dependencies, adding Docker’s repository 
and GPG key, installing Docker, and enabling its service. 
Screenshot: 

# 4. Jenkins Job 1: Install_Docker_Ansible 
This job runs the Ansible playbook on the slave node to prepare it for Docker 
deployments. 
 Configured with Git repo (or local playbook path). 
 Poll SCM enabled for automatic triggering. 
 Executes ansible-playbook command. 
Screenshots:

# 5. Jenkins Job 2: Build_PHP_Docker_Image 
Job 2 clones the PHP project and builds the Docker image: 
 Git repo URL and branch configured. 
 Runs docker build -t my-php-app . in the workspace root. 
Screenshots:

# 6. Jenkins Job 3: Deploy_PHP_Docker_Container 
Job 3 stops/removes existing containers and deploys a new container exposing port 
8080: 
bash 
docker stop my-php-app || true 
docker rm my-php-app || true 
docker run -d --name my-php-app -p 8080:80 my-php-app 
Screenshots:

# 7. Jenkins Job 4: Cleanup_Docker_Container 
Handles cleanup on deployment failure by stopping and removing the Docker container 
to maintain system hygiene. 
Screenshots: 

# 8. Job Chaining and Pipeline Flow 
Jobs are chained using Post-build Actions, enabling fully automated progression: 
 Job 1 triggers Job 2 on success. 
 Job 2 triggers Job 3 on success. 
 Job 3 triggers Job 4 on failure. 
Screenshot:

# 9. Triggering Mechanism via Poll SCM 
Jenkins polls the Git repository every minute (H/1 * * * *), identifying new commits and 
triggering Job 1 automatically, bypassing the complexity of setting up webhooks or 
exposing Jenkins publicly. 
Screenshot: 

# 10. Running the Pipeline 
Code pushes to GitHub trigger the pipeline, with builds appearing in Jenkins Dashboard 
and job console outputs showing each stage’s success, culminating in a deployed 
Dockerized PHP application accessible via port 8080 on the slave node. 
Screenshots: 

# 11. Troubleshooting and Solutions 
Encountered issues included: 
 SSH key verification errors solved by manual key acceptance. 
 Password prompts on sudo fixed by granting passwordless sudo to Jenkins user. 
 Apt lock conflicts resolved by killing stalled processes and removing lock files.

# Conclusion 
This project successfully implements an automated Jenkins pipeline integrating Ansible 
and Docker to build and deploy a PHP web app. The approach ensures consistent, 
repeatable deployments, reducing manual eAort and errors.