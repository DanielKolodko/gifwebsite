pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/DanielKolodko/gifwebsite.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    // Build Docker image
                    bat 'docker build -t gifwebsite .'
                    echo 'Building the project completed'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Deploy Docker container
                    bat 'docker run -d -p 5000:5000 --name gifwebsite gifwebsite'
                    echo 'Deploying the project'

                    // Wait for the container to be ready
                    bat '''
                    :loop
                    curl http://localhost:5000 || (
                        echo Waiting for app to start...
                        timeout /t 5 > nul
                        goto :loop
                    )
                    '''
                }
            }
        }
    }
    post {
        always {
            script {
                echo 'Cleaning up Docker resources...'
                
                // Stop and remove the Docker container if it exists
                bat '''
                docker ps -q --filter "name=gifwebsite" | findstr . && docker stop gifwebsite && docker rm gifwebsite || echo "No container to clean"
                '''
                
                // Remove the Docker image if needed
                bat '''
                docker images -q gifwebsite | findstr . && docker rmi gifwebsite || echo "No image to remove"
                '''
            }
            echo 'Cleanup completed.'
        }
        failure {
            echo 'Pipeline failed! Check the logs for more details.'
        }
    }
}



