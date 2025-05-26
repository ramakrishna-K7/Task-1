# Task-1
CI/CD Deployment Guide for Task-1



STEP1:

LAUNCH THE  EC2 INSTANCE 
1.	AMI: Amazon Linux Kernel 5.10
2.	Instance Type: t2.micro
3.           EBS: 8GB

STEP-2:

Install docker
 yum install docker -y && systemctl start docker
Chmod 777 //var/run/docker.sock

STEP-3:

 Install Jenkins
     * yum install Jenkins -y && systemctl start Jenkins
install the plugins in Jenkins
     * Go to the d ashboard → manage Jenkins → plugins → available plugins
Plugins:
    1. nodejs 
    2. eclipse
    3.pipeline stage view

configure the plugins in global tool configuration
    * Go to the dashboard → manage Jenkins → tools
          Goto the nodejs installation and click install automatically select the version of 16.2.0
   
 Go to the jdk and click install automatically select the of java- 17.0.8.1+1
 
  git 
  select install automatically


  ADD the tool credentials in jenkins
 * go to the dashboard → manage jenkins → credentials → system → globel credentials → add credentials 
 Git --
Username: Ramakrishna-k7
Passwd: $$$$
Credentials id : git
   
Docker--
username: ramakrishna737
password: docker hub-password
credentials id : docker-passwd  

STEP-4:

*Go to the GitHub repository the folder should be like 
github/
└── workflows/
       └── main.yml          ← GitHub Actions workflow
── Dockerfile
── index.js


dockerfile
 ![image](https://github.com/user-attachments/assets/6e9f6c53-88ea-4589-adf3-e2c3a55defff)


Normal  Index.js file 
 ![image](https://github.com/user-attachments/assets/061fd024-807b-4f91-aced-e7fd3a9cbf64)


Add the webhook this ensure your pipeline trigger job whenever code pushed into the repository

Goto the GitHub →  settings →  webhooks  →  add webhook




STEP-3  
 write the jenkins pipeline in main.yml file which is located in Git 

 pipeline {
    agent any
    tools{
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
                    withDockerRegistry(credentialsId: '8ed086e0-0f2e-451d-9a4a-834b851156be') {
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
![image](https://github.com/user-attachments/assets/702ddb97-e8ed-457b-a2a8-8e83e0d29c77)





    
