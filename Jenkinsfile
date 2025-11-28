pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'bose2001'
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/mean-frontend"
        BACKEND_IMAGE = "${DOCKERHUB_USER}/mean-backend"
        SERVER_IP = "3.230.166.189"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Bose2001/crud-dd-task-mean-app.git'
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build --prod'
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh "docker build -t mean-backend ./backend"
                sh "docker build -t mean-frontend ./frontend"
            }
        }

        stage('Tag Docker Images') {
            steps {
                sh "docker tag mean-backend ${BACKEND_IMAGE}:latest"
                sh "docker tag mean-frontend ${FRONTEND_IMAGE}:latest"
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                sh "docker push ${BACKEND_IMAGE}:latest"
                sh "docker push ${FRONTEND_IMAGE}:latest"
            }
        }

        stage('Deploy on Ubuntu Server') {
            steps {
                sshagent(credentials: ['ubuntu-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@${SERVER_IP} '
                        cd ~/crud-dd-task-mean-app &&
                        docker compose down &&
                        docker compose pull &&
                        docker compose up -d
                    '
                    """
                }
            }
        }
    }
}

