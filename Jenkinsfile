node {
    // Clean workspace before doing anything
    deleteDir()

    try {
        stage ('SCM Check') {
             git branch: 'master', credentialsId: 'jenkins_ssh_key', url: 'git@github.com:venurachakonda/simple-java-maven-app.git'
             stash includes: '**', name: 'sampleApp'
        }

        stage ('Clone Docker Dependencies') {
          node{
            git branch: 'master', credentialsId: 'jenkins_ssh_key', url: 'git@github.com:venurachakonda/pipeline_scripts.git'
            stash includes: '**', name: 'dockerConfig'
          }
        }

         /* Requires the Docker Pipeline plugin to be installed */
        docker.image('maven:3-alpine').inside('-v $HOME/.m2:/root/.m2') {
            stage('Build Maven package') {
                sh 'mvn -B -DskipTests clean package'
            }

        }            

        stage ('Setup Build Directory') {
          node{
            dir("test1"){
              unstash 'dockerConfig'
              sh "ls -ltra"
            }
          }
        }          

        stage ('Build Docker Image') {
          node{
            dir("test1"){
              container = docker.build('sampleApp', ".")
            }
          }
        }
        stage ("Push Docker Image") {
          node{
            dir("test1"){
              docker.withRegistry('http://192.168.33.50', 'docker_registry') {
                container.push("v${env.BUILD_NUMBER}")
                container.push('latest')
              }
            }
            milestone 1
          }
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
        

        /*stage ('Deploy') {
            sh "echo 'shell scripts to deploy to server...'"
        }*/
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}