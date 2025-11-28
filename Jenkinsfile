pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'bose2001'
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/mean-frontend"
        BACKEND_IMAGE = "${DOCKERHUB_USER}/mean-backend"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Bose2001/crud-dd-task-mean-app.git'
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    sh 'docker build -t mean-frontend ./frontend'
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    sh 'docker build -t mean-backend ./backend'
                }
            }
        }

        stage('Tag Images for DockerHub') {
            steps {
                script {
                    sh "docker tag mean-frontend ${FRONTEND_IMAGE}:latest"
                    sh "docker tag mean-backend ${BACKEND_IMAGE}:latest"
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKERHUB_PASS')]) {
                    sh "echo $DOCKERHUB_PASS | docker login -u ${DOCKERHUB_USER} --password-stdin"
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                script {
                    sh "docker push ${FRONTEND_IMAGE}:latest"
                    sh "docker push ${BACKEND_IMAGE}:latest"
                }
            }
        }
    }
}
