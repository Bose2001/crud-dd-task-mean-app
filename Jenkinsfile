pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "bose2001"
        BACKEND_IMAGE = "bose2001/mean-backend"
        FRONTEND_IMAGE = "bose2001/mean-frontend"
        SERVER_IP = "3.230.166.189"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/Bose2001/crud-dd-task-mean-app.git'
            }
        }

        stage('Build Angular Frontend') {
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
                script {
                    sh "docker build -t $BACKEND_IMAGE:latest ./backend"
                    sh "docker build -t $FRONTEND_IMAGE:latest ./frontend"
                }
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
                sh "docker push $BACKEND_IMAGE:latest"
                sh "docker push $FRONTEND_IMAGE:latest"
            }
        }

        stage('Deploy to Ubuntu Server') {
            steps {
                script {
                    sshagent(credentials: ['ubuntu-ssh']) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@$SERVER_IP '
                            cd crud-dd-task-mean-app &&
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
}
