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
                    powershell '''
                        try {
                            docker build -t ${DOCKER_IMAGE} .
                        } catch {
                            Write-Host "Docker build failed"
                            exit 1
                        }
                    '''
                }
            }
        }
        stage('Test Docker Image') {
            steps {
                script {
                    echo 'Skipping actual tests, running placeholder...'
                    powershell '''
                        try {
                            docker run danielkolodko/gifwebsite pytest --help
                        } catch {
                            Write-Host "Test run failed"
                            exit 1
                        }
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
                            try {
                                docker login -u $Env:DOCKER_USER -p $Env:DOCKER_PASSWORD
                            } catch {
                                Write-Host "Docker login failed"
                                exit 1
                            }
                        '''
                        echo 'Pushing Docker image...'
                        powershell '''
                            try {
                                docker push ${DOCKER_IMAGE}
                            } catch {
                                Write-Host "Docker push failed"
                                exit 1
                            }
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
                        try {
                            docker-compose -f ${DOCKER_COMPOSE_FILE} up -d
                        } catch {
                            Write-Host "Docker Compose up failed"
                            exit 1
                        }
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
