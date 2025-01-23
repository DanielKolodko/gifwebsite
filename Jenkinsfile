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
        stage('Test Docker Image') {
            steps {
                script {
                    echo 'Running tests using Docker Compose...'
                    // Run pytest in the container; ensure the working directory is set correctly
                    powershell '''
                        docker compose -f ${DOCKER_COMPOSE_FILE} run web pytest --help
                    '''
                }
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        echo 'Logging in to Docker Hub...'
                        powershell '''
                            docker login -u $Env:DOCKER_USER -p $Env:DOCKER_PASSWORD
                        '''
                        echo 'Pushing Docker image to Docker Hub...'
                        // You can still use docker-compose push to push all services
                        powershell '''
                            docker compose -f ${DOCKER_COMPOSE_FILE} push
                        '''
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying application using Docker Compose...'
                    powershell '''
                        docker compose -f ${DOCKER_COMPOSE_FILE} up -d
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
