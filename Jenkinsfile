pipeline {
    agent any

    environment {
        APP_NAME = "onlinebookstore"
        REGISTRY = "your-dockerhub-username"
        BRANCH_NAME = "${env.GIT_BRANCH}".replaceAll('/', '-')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-org/onlinebookstore.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${REGISTRY}/${APP_NAME}:${BRANCH_NAME}-${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${REGISTRY}/${APP_NAME}:${BRANCH_NAME}-${env.BUILD_NUMBER}"
                }
            }
        }

        stage('Deploy to Dev') {
            when {
                branch 'main'
            }
