properties([
  buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), 
  parameters([
    string(defaultValue: 'sampleApp', description: 'Application Name', name: 'APP_NAME', trim: true),
    string(defaultValue: 'git@github.com:venurachakonda/simple-java-maven-app.git', description: 'Application Source URL', name: 'APP_SCM_URL', trim: true), 
    string(defaultValue: 'git@github.com:venurachakonda/pipeline_scripts.git', description: 'Rackspace template files -  Docker dependencies', name: 'RPS_SCM_URL', trim: true)
  ])
])


stage ('Clone Application Code') {
  node{
     git branch: 'master', credentialsId: 'jenkins_ssh_key', url: params.APP_SCM_URL
     stash includes: '**', name: params.APP_NAME    
  }

}

stage ('Clone Docker Dependencies') {
  node{
    git branch: 'master', credentialsId: 'jenkins_ssh_key', url: params.RPS_SCM_URL
    stash includes: '**', name: 'dockerConfig'
  }
}


stage ('Setup Build Directory') {
  node{
    dir("build"){
      unstash 'dockerConfig'
      sh "mv docker/* ."
    }
    dir("build/${params.APP_NAME}"){
      unstash params.APP_NAME
    }             
  }
} 

/*
stage('Build jars') {
  node('docker') {
    echo 'Building jars ...'
    def maven = docker.image('maven:3.5.3-jdk-8')
    maven.pull()
    maven.inside {
      checkout scm
      sh 'mvn clean package -DskipTests'
      try {
        sh 'mvn test'
      }
      finally {
        junit '*/ /*/target/surefire-reports/TEST-*.xml'
      }
      def artifacts = 'devicehive-backend/target/devicehive-backend-*-boot.jar, devicehive-auth/target/devicehive-auth-*-boot.jar, devicehive-plugin/target/devicehive-plugin-*-boot.jar, devicehive-frontend/target/devicehive-frontend-*-boot.jar, devicehive-common/target/devicehive-common-*-shade.jar'
      archiveArtifacts artifacts: artifacts, fingerprint: true, onlyIfSuccessful: true
      stash includes: artifacts, name: 'jars'
    }
  }
}

stage('Build and publish Docker images in CI repository') {
  node('docker') {
    echo 'Building images ...'
    unstash 'jars'
    def auth = docker.build('devicehiveci/devicehive-auth:${BRANCH_NAME}', '--pull -f dockerfiles/devicehive-auth.Dockerfile .')
    def plugin = docker.build('devicehiveci/devicehive-plugin:${BRANCH_NAME}', '-f dockerfiles/devicehive-plugin.Dockerfile .')
    def frontend = docker.build('devicehiveci/devicehive-frontend:${BRANCH_NAME}', '-f dockerfiles/devicehive-frontend.Dockerfile .')
    def backend = docker.build('devicehiveci/devicehive-backend:${BRANCH_NAME}', '-f dockerfiles/devicehive-backend.Dockerfile .')
    def hazelcast = docker.build('devicehiveci/devicehive-hazelcast:${BRANCH_NAME}', '--pull -f dockerfiles/devicehive-hazelcast.Dockerfile .')

    echo 'Pushing images to CI repository ...'
    docker.withRegistry('https://registry.hub.docker.com', 'devicehiveci_dockerhub'){
      auth.push()
      plugin.push()
      frontend.push()
      backend.push()
      hazelcast.push()
    }
  }
}*/