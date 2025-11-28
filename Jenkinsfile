pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Jenkins credential ID for Docker Hub
        DOCKERHUB_USERNAME = 'bose2001'
        FRONTEND_IMAGE = "bose2001/mean-frontend"
        BACKEND_IMAGE  = "bose2001/mean-backend"
        SERVER_IP = "3.230.166.189"
        APP_PATH = "/home/ubuntu/crud-dd-task-mean-app"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                deleteDir() // clear Jenkins workspace to avoid disk space issues
            }
        }

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Bose2001/crud-dd-task-mean-app.git', branch: 'main', credentialsId: 'github-creds'
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm ci'
                    sh 'npm run build --prod'
                }
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'npm ci --only=production'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh "docker build -t $FRONTEND_IMAGE ./frontend"
                sh "docker build -t $BACKEND_IMAGE ./backend"
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$DOCKERHUB_CREDENTIALS", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push $FRONTEND_IMAGE"
                    sh "docker push $BACKEND_IMAGE"
                }
            }
        }

        stage('Deploy on Server') {
            steps {
                sshagent(['ubuntu-ssh']) { // Jenkins SSH credential ID for server
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@$SERVER_IP '
                        cd $APP_PATH
                        sudo docker-compose pull
                        sudo docker-compose up -d
                    '
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed. Check logs!'
        }
    }
}
