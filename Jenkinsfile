pipeline {
  agent any
// Retention policy to dictate jenkins to keep 2 log and keep 1 artifact
// pay attention to the syntax, that is camel case in numToKeepStr
  options{
    buildDiscarder(logRotator(numToKeepStr:'2', artifactnumToKeepStr: '1'))
  }

  stages {
    stage('build') {
      steps {
        sh 'ant -f build.xml -v'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
