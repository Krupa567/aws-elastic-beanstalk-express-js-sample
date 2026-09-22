pipeline {
    agent none

    stages {
        stage('Install Dependencies') {
            agent {
                docker { image 'node:16' }
            }
            steps {
                sh 'npm install'
            }
        }

        stage('Run Unit Tests') {
            agent {
                docker { image 'node:16' }
            }
            steps {
                sh 'npm test'
            }
        }

        stage('Security Scan - npm audit') {
            agent {
                docker { image 'node:16' }
            }
            steps {
                sh 'npm audit --audit-level=high'
            }
        }

        stage('Build and Push Docker Image') {
            agent any
            environment {
                IMAGE_NAME = 'krupa23121609/aws-eb-express-sample'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
                    sh 'printf "%s" "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }
    }
}
