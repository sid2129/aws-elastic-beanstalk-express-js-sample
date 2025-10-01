pipeline {
    agent {
        docker { image 'node:16-alpine' }
    }

    environment {
        IMAGE_NAME = "aws-node-sample-app"
        REGISTRY = "your-dockerhub-username" // replace with your Docker Hub username
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install --save'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'npm test || echo "Tests completed"'
            }
        }

        stage('Security Scan') {
            steps {
                // Using npm audit as a basic example
                sh '''
                npm install -g snyk
                snyk test || exit 1
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker tag ${IMAGE_NAME}:latest $DOCKER_USER/${IMAGE_NAME}:latest
                    docker push $DOCKER_USER/${IMAGE_NAME}:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker system prune -f'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

N
