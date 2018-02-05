pipeline {
// make the whole pipeline run in the master node as it has apache installed and configured
//Also make sure the Jenkins master has label set as 'master'
// Sperate agent on different stages:
  agent none

  stages {
    stage ('Unit Tests') {
      agent {
        // master node with apache label
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }

    stage('build') {
      agent {
        // master node with apache label
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }

    stage('deploy') {
      agent {
        // master node with apache label
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all"
      }
    }

    stage("Running on Centos") {
      agent {
        // master node with apache label
        label 'Centos'
      }
      steps {
        sh "wget http://fishereatworld3.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    // adding a docker debian agent for functional testing
    stage("Test on Debian") {
      agent {
        docker 'openjdk:8u151-jre'
      }
      steps {
        sh "wget http://fishereatworld3.mylabserver.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    // comment:automate the promation process
    stage('Promote to Green') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
      }

    }
  }


}
