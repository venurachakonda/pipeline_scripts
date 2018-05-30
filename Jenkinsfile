node {
    // Clean workspace before doing anything
    deleteDir()

    try {
        stage ('Clone App Code') {
             git branch: 'master', credentialsId: 'jenkins_ssh_key', url: 'git@github.com:venurachakonda/simple-java-maven-app.git'
             stash includes: '**', name: 'sampleApp'
        }

         /* Requires the Docker Pipeline plugin to be installed */
        docker.image('maven:3-alpine').inside('-v $HOME/.m2:/root/.m2') {
            stage('Build') {
                sh 'mvn --version'
            }

            stage ('Tests') {
                parallel 'static': {
                    sh "echo 'shell scripts to run static tests...'"
                },
                'unit': {
                    sh "echo 'shell scripts to run unit tests...'"
                },
                'integration': {
                    sh "echo 'shell scripts to run integration tests...'"
                }
            }

        }        

        /*stage ('Deploy') {
            sh "echo 'shell scripts to deploy to server...'"
        }*/
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}