pipeline {
  agent any
// Retention policy to dictate jenkins to keep 2 log and keep 1 artifact
// pay attention to the syntax, that is camel case in numToKeepStr
  options{
    buildDiscarder(logRotator(numToKeepStr:'2', artifactNumToKeepStr: '1'))
  }

  stages {
    stage ('Unit Tests') {
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
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
