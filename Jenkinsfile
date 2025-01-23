pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'danielkolodko/gifwebsite'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DanielKolodko/gifwebsite.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                powershell 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Test Docker Image') {
            steps {
                powershell 'docker run --rm $DOCKER_IMAGE pytest tests/'
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    powershell 'docker login -u $DOCKER_USER -p $DOCKER_PASSWORD'
                    powershell 'docker push $DOCKER_IMAGE'
                }
            }
        }
        stage('Deploy') {
            steps {
                powershell 'docker-compose up -d'
            }
        }
    }
    post {
        always {
            echo 'Cleaning up...'
            powershell 'docker system prune -f'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

