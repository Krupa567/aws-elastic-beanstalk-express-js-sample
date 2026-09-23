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
            steps {
                sh 'docker build -t krupa23121609/aws-eb-express-sample:$BUILD_NUMBER .'
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        printf '%s' "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push krupa23121609/aws-eb-express-sample:$BUILD_NUMBER
                    '''
                }
            }
        }
    }
}
