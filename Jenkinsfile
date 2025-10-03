pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDS = credentials('dockerhub-credentials')
        SNYK_TOKEN = credentials('snyk-token')
    }
    stages {
        stage('Install Dependencies & Run Tests') {
            agent {
                docker {
                    image 'node:16-alpine'
                    args '-v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'npm install --save'
                sh 'npm test || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                echo $DOCKER_HUB_CREDS_PSW | docker login -u $DOCKER_HUB_CREDS_USR --password-stdin
                docker build -t ${DOCKER_HUB_CREDS_USR}/aws-nodejs-sample:latest .
                docker push ${DOCKER_HUB_CREDS_USR}/aws-nodejs-sample:latest
                """
            }
        }

        stage('Security Scan') {
            agent {
                docker {
                    image 'node:16-alpine'
                }
            }
            steps {
                sh """
                npm install -g snyk
                snyk auth $SNYK_TOKEN
                snyk test --severity-threshold=high
                """
            }
        }
    }
}

