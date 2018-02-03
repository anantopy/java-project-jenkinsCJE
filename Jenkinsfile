pipeline {
// make the whole pipeline run in the master node as it has apache installed and configured
//Also make sure the Jenkins master has label set as 'master'
  agent {
    label 'master'
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
    stage('deploy') {
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all"
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
