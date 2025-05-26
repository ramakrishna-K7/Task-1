CI/CD Deployment Guide for Task-1
This guide outlines the steps to set up a CI/CD pipeline using Jenkins and GitHub for deploying a Node.js web application with Docker.
________________________________________
STEP 1: Launch EC2 Instance
•	AMI: Amazon Linux Kernel 5.10
•	Instance Type: t2.micro
•	EBS Volume: 8GB
________________________________________
STEP 2: Install Docker
yum install docker -y
systemctl start docker
chmod 777 /var/run/docker.sock
________________________________________
STEP 3: Install Jenkins
yum install jenkins -y
systemctl start jenkins
Install Jenkins Plugins
Go to:
•	Dashboard → Manage Jenkins → Plugins → Available Plugins
Install the following plugins:
1.	NodeJS
2.	Eclipse
3.	Pipeline Stage View
   
Configure Plugins (Global Tool Configuration)
Go to:
•	Dashboard → Manage Jenkins → Global Tool Configuration
•	NodeJS: Check "Install automatically" and select version 16.2.0
 
•	JDK: Check "Install automatically" and select version 17.0.8.1+1
 
•	Git: Check "Install automatically"

Add Credentials in Jenkins
Go to:
•	Dashboard → Manage Jenkins → Credentials → System → Global Credentials → Add Credentials
Git Credentials:
•	Username: Ramakrishna-k7
•	Password: (GitHub password or token)
•	Credentials ID: git
DockerHub Credentials:
•	Username: ramakrishna737
•	Password: (DockerHub password)
•	Credentials ID: docker-passwd
________________________________________
STEP 4: Prepare GitHub Repository
Project structure:
github/
└── workflows/
    └── main.yml         # GitHub Actions workflow (if used)
Dockerfile
index.js               
Sample Dockerfile
![image](https://github.com/user-attachments/assets/e72a7d2a-35c4-448c-9e5d-0d1bb77d516e)

 
Sample index.js
![image](https://github.com/user-attachments/assets/b2b5f71f-d38b-4d0d-820f-077d2ce9882c)

 ________________________________________
STEP 5: Configure Webhook
Enable GitHub Webhook to trigger Jenkins builds:
1.	Go to GitHub Repository → Settings → Webhooks → Add Webhook
2.	Payload URL: http://<your-jenkins-url>/github-webhook/
3.	Content Type: application/json
4.	Trigger: Only Push events
________________________________________
STEP 6: Jenkins Pipeline Script
Use this script in your Jenkins Pipeline job configuration:
pipeline {
    agent any

    tools {
        jdk 'java17'
        nodejs 'node18'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git credentialsId: 'Github_user1', url: 'https://github.com/ramakrishna-K7/Task-1.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Create Docker Image') {
            steps {
                sh 'docker build -t ramakrishna-k7/app:v1 .'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-passwd') {
                        sh 'docker push ramakrishna-k7/app:v1'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d -p 3000:3000 ramakrishna-k7/app:v1'
            }
        }
    }
}

![image](https://github.com/user-attachments/assets/55fb7c43-9a87-4045-b332-767491d09cc1)

________________________________________
 
