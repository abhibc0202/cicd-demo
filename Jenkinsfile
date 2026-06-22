pipeline {
    agent any

    environment {
        ECR = "082319703873.dkr.ecr.ap-south-2.amazonaws.com/cicd-demo"
        DOCKER_HOST = "ubuntu@98.130.122.157"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/abhibc0202/cicd-demo.git'
            }
        }

        stage('Build Docker Image on Docker Server') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@98.130.122.157 "
                cd ~/cicd-demo || git clone https://github.com/abhibc0202/cicd-demo.git cicd-demo
                cd cicd-demo &&
                git pull origin main &&
                docker build -t cicd-demo .
                "
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                ssh ubuntu@98.130.122.157 "
                docker tag cicd-demo:latest $ECR:latest
                "
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                ssh ubuntu@98.130.122.157 "
                aws ecr get-login-password --region ap-south-2 | docker login --username AWS --password-stdin 082319703873.dkr.ecr.ap-south-2.amazonaws.com
                "
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                ssh ubuntu@98.130.122.157 "
                docker push $ECR:latest
                "
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                ssh ubuntu@98.130.122.157 "
                docker stop app || true &&
                docker rm app || true &&
                docker run -d -p 80:80 --name app $ECR:latest
                "
                '''
            }
        }
    }
}
