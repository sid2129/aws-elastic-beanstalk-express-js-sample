pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDS = credentials('dockerhub-credentials')
        SNYK_TOKEN = credentials('snyk-token') // only if using Snyk
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --save'
            }
        }

        stage('Run Unit Tests') {
            steps {
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

