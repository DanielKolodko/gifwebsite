stage('Test Docker Image') {
    steps {
        script {
            echo 'Running tests using Docker Compose...'
            powershell '''
                docker-compose -f ${DOCKER_COMPOSE_FILE} run web pytest --help
            '''
        }
    }
}
