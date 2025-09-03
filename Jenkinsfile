pipeline {
    agent any

    environment {
        APP_NAME = "online-book-store1"
        IMAGE_NAME = "online-book-store1:v1"
        CONTAINER_NAME = "bookstore1"
        HOST_PORT = "8082"
        CONTAINER_PORT = "8080"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/anithavalluri02/onlinebookstore1.git/'
            }
        }

        stage('Check Workspace') {
            steps {
                sh 'ls -R'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker rmi -f ${IMAGE_NAME} || true"
                sh "docker build -t ${IMAGE_NAME} -f Dockerfile ."
            }
        }

        stage('Deploy Container') {
            steps {
                sh "docker rm -f ${CONTAINER_NAME} || true"
                sh "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}"
            }
        }

        stage('Check Deployment') {
            steps {
                sh "docker ps | grep ${CONTAINER_NAME}"
            }
        }
    }

    post {
        failure {
            echo "❌ Deployment failed. Check logs."
        }
        success {
            echo "✅ Deployment successful."
        }
    }
}
