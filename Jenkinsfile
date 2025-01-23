pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'gifwebsite'  // Docker image name
        DOCKER_COMPOSE_FILE = 'docker-compose.yml' // Path to your docker-compose file
        REPO_URL = 'https://github.com/DanielKolodko/gifwebsite.git' // Your repository URL
        BRANCH = 'main' // Branch to build
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo 'Checking out the code from Git repository...'
                    git branch: "${BRANCH}", url: "${REPO_URL}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    powershell '''
                        docker --version
                        docker build -t ${DOCKER_IMAGE} .
                    '''
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    echo 'Running Docker image tests...'
                    powershell '''
                        docker run ${DOCKER_IMAGE} pytest --help
                    '''
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        echo 'Logging into Docker Hub...'
                        powershell '''
                            docker login -u $Env:DOCKER_USER -p $Env:DOCKER_PASSWORD
                        '''
                        echo 'Pushing Docker image to Docker Hub...'
                        powershell '''
                            docker push ${DOCKER_IMAGE}
                        '''
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    echo 'Deploying application with Docker Compose...'
                    powershell '''
                        docker-compose -f ${DOCKER_COMPOSE_FILE} up
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker resources...'
            powershell '''
                # Manually stop and remove containers
                docker ps -a -q --filter "name=${DOCKER_IMAGE}" | ForEach-Object { docker rm -f $_ }
                
                # Clean up unused Docker resources (images, volumes, etc.)
                docker system prune -f
            '''
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed, please check the logs for details.'
        }
    }
}

