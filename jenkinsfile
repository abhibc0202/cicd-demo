pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/<your-repo>.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t cicd-demo .'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag cicd-demo:latest 082319703873.dkr.ecr.ap-south-2.amazonaws.com/cicd-demo:latest'
            }
        }

        stage('Login ECR') {
            steps {
                sh 'aws ecr get-login-password --region ap-south-2 | docker login --username AWS --password-stdin 082319703873.dkr.ecr.ap-south-2.amazonaws.com'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push 082319703873.dkr.ecr.ap-south-2.amazonaws.com/cicd-demo:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                ssh ubuntu@98.130.122.157 "
                docker pull 082319703873.dkr.ecr.ap-south-2.amazonaws.com/cicd-demo:latest &&
                docker stop app || true &&
                docker rm app || true &&
                docker run -d -p 80:80 --name app 082319703873.dkr.ecr.ap-south-2.amazonaws.com/cicd-demo:latest
                "
                '''
            }
        }
    }
}
