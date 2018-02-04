pipeline {
// make the whole pipeline run in the master node as it has apache installed and configured
//Also make sure the Jenkins master has label set as 'master'
// Sperate agent on different stages:
  agent none

  stages {
    stage ('Unit Tests') {
      steps {
        agent {
          // master node with apache label
          label 'apache'
        }
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      steps {
        agent {
          // master node with apache label
          label 'apache'
        }
        sh 'ant -f build.xml -v'
      }
    }
    stage('deploy') {
      steps {
        agent {
          // master node with apache label
          label 'apache'
        }
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all"
      }
    }
    stage("Running on Centos") {
      agent {
        // master node with apache label
        label 'Centos'
      }
      steps {
        sh "wget http://fishereatworld3.mylabserver.com:8080/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "jar -jar rectangle_${env.BUILD_NUMBER.jar 3 4}"
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
