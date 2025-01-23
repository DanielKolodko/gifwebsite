pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'danielkolodko/cat-gif-website1-flask-app:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/DanielKolodko/gifwebsite.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    echo 'Running Docker container to test...'
                    sh 'docker run --rm -d --name test-container -p 5000:5000 ${DOCKER_IMAGE}'
                    sh 'sleep 10'  // Allow some time for the container to start
                    sh 'curl -f http://127.0.0.1:5000'  // Ensure the app is running
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying application...'
                    // Add your deployment steps here (e.g., pushing to Kubernetes, EC2, etc.)
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            sh 'docker stop test-container || true'
        }
    }
}
