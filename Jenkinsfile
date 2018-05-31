properties([
  buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')), 
  parameters([
    string(defaultValue: 'sampleApp', description: 'Application Name', name: 'APP_NAME', trim: true),
    string(defaultValue: '192.168.33.50', description: 'Docker Registry Endpoint', name: 'DOCKER_REGISTRY', trim: true),    
    string(defaultValue: 'git@github.com:venurachakonda/spring-petclinic.git', description: 'Application Source URL', name: 'APP_SCM_URL', trim: true), 
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


stage('Build jars') {
  node{
    echo 'Building jars ...'
    dir("build/${params.APP_NAME}"){
      def maven = docker.image('maven:3.5.3-jdk-8')
      maven.pull()
      maven.inside {
        sh 'mvn clean install'
        try {
          sh 'mvn test'
        }
        finally {
          echo "surefire-reports here"
          junit '**/target/surefire-reports/TEST-*.xml'
        }
        def artifacts = "**/target/*.jar"
        archiveArtifacts artifacts: artifacts, fingerprint: true, onlyIfSuccessful: true
        stash includes: artifacts, name: 'jars'
      }
      def appname = sh(script: 'basename $(ls target/*jar) ', returnStdout: true)
      println appname
    }    
  }
}


stage('Build and publish Docker images to registry') {
  node() {
    echo 'Building images ...'
    dir("build"){
      unstash 'jars'
      def appImage = docker.build("${params.APP_NAME}", "--build-arg appname=${appname} .")
  
      echo 'Pushing images to registry ...'
      docker.withRegistry("http://${params.DOCKER_REGISTRY}", 'docker_registry'){
        appImage.push("v${env.BUILD_NUMBER}")
        appImage.push("latest")
      }      
    }
  }
}