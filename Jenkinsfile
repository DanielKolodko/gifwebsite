pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'danielkolodko/gifwebsite'
        DOCKER_COMPOSE_FILE = 'docker-compose.yml' // Specify your compose file
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
                    echo 'Building Docker Image...'
                    powershell "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        stage('Test Docker Image') {
            steps {
                script {
                    echo 'Skipping actual tests, running placeholder...'
                    powershell '''
                    docker run danielkolodko/gifwebsite pytest --help
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
                        docker login -u $Env:DOCKER_USER -p $Env:DOCKER_PASSWORD || exit 1
                        '''
                        echo 'Pushing Docker image...'
                        powershell "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying application using Docker Compose...'
                    powershell "docker-compose -f ${DOCKER_COMPOSE_FILE} up -d"
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