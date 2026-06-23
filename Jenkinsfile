pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '082319703873'
        AWS_REGION = 'ap-south-2'
        IMAGE_NAME = 'cicd-demo'
        ECR_REPO = '082319703873.dkr.ecr.ap-south-2.amazonaws.com/cicd-demo'
        DOCKER_SERVER = '16.112.18.208'
    }

    stages {

        stage('Build Docker Image on Docker Server') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ubuntu@${DOCKER_SERVER} '
                cd ~/cicd-demo || git clone https://github.com/abhibc0202/cicd-demo.git
                cd ~/cicd-demo
                git pull origin main
                docker build -t ${IMAGE_NAME} .
                '
                """
            }
        }

        stage('Tag Image') {
            steps {
                sh """
                ssh ubuntu@${DOCKER_SERVER} '
                docker tag ${IMAGE_NAME}:latest ${ECR_REPO}:latest
                '
                """
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                ssh ubuntu@${DOCKER_SERVER} '
                aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                '
                """
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                ssh ubuntu@${DOCKER_SERVER} '
                docker push ${ECR_REPO}:latest
                '
                """
            }
        }

        stage('Deploy Container') {
            steps {
                sh """
                ssh ubuntu@${DOCKER_SERVER} '
                docker stop app || true
                docker rm app || true
                docker pull ${ECR_REPO}:latest
                docker run -d --name app -p 80:80 ${ECR_REPO}:latest
                '
                """
            }
        }
    }
}
