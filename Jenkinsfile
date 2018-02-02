pipeline {
  agent any
// Retention policy to dictate jenkins to keep 2 log and keep 1 artifact
  options{
    buildDiscarder(logRotator(numToKeepStr:'2', artifactNumtoKeepStr: '1'))
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
