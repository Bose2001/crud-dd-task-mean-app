pipeline {
    agent any

    environment {
        NODEJS_HOME = '/usr/bin' // system Node path
        PATH = "${NODEJS_HOME}:${env.PATH}"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/Bose2001/crud-dd-task-mean-app.git', branch: 'main', credentialsId: 'github-creds'
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
                sh 'docker build -t mean-app-frontend ./frontend'
                sh 'docker build -t mean-app-backend ./backend'
            }
        }

        stage('Tag Docker Images') {
            steps {
                sh 'docker tag mean-app-frontend <DOCKERHUB_USERNAME>/mean-app-frontend:latest'
                sh 'docker tag mean-app-backend <DOCKERHUB_USERNAME>/mean-app-backend:latest'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                sh 'docker push <DOCKERHUB_USERNAME>/mean-app-frontend:latest'
                sh 'docker push <DOCKERHUB_USERNAME>/mean-app-backend:latest'
            }
        }

        stage('Deploy on Ubuntu Server') {
            steps {
                sh 'ssh ubuntu@<SERVER_IP> "docker pull <DOCKERHUB_USERNAME>/mean-app-frontend:latest && docker pull <DOCKERHUB_USERNAME>/mean-app-backend:latest && docker-compose -f /home/ubuntu/mean-app/docker-compose.yml up -d"'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}

