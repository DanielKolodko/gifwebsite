pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'gifwebsite:latest'  // Include a tag for your image
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'  // Specify your compose file
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DanielKolodko/gifwebsite.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker Image using Docker Compose...'
                    // Building using docker-compose build to ensure all services are built
                    powershell '''
                        echo "Using docker-compose to build the image"
                        docker compose -f ${DOCKER_COMPOSE_FILE} build
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying application using Docker Compose...'
                    powershell '''
                        docker compose -f ${DOCKER_COMPOSE_FILE} up --detach
                    '''
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up Docker resources...'
            powershell 'docker system prune -f'
        }
        failure {
            echo 'Pipeline failed! Check the logs for more details.'
        }
    }
}
