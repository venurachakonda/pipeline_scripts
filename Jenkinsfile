node {
    /* Requires the Docker Pipeline plugin to be installed */
    docker.image('maven:3-alpine').inside {
        stage('Build') {
            sh 'mvn --version'
        }
    }
}