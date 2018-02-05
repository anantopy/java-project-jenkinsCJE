pipeline {
// make the whole pipeline run in the master node as it has apache installed and configured
//Also make sure the Jenkins master has label set as 'master'
// Sperate agent on different stages:
  agent none
  environment {
    MAJOR_VERSION = 1
  }

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
        sh "if ! [ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }

    stage("Running on Centos") {
      agent {
        // master node with apache label
        label 'Centos'
      }
      steps {
        sh "wget http://fishereatworld3.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    // adding a docker debian agent for functional testing
    stage("Test on Debian") {
      agent {
        docker 'openjdk:8u151-jre'
      }
      steps {
        sh "wget http://fishereatworld3.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    //Move the jar file to green folder
    stage('Promote to Green') {
      agent {
        label 'apache'
      }
      //Execute the stage when the branch being built matches 'master'
      when {
        branch 'master'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }

    // comment:automate the code promation process
    stage('Prmote development branch to master') {
      agent {
        label 'apache'
      }
      //Execute the stage when the branch being built matches 'development'
      when {
        branch 'development'
      }
      steps {
        echo "Stashing any local changes"
        sh "git stash"
        echo "Checking Out development"
        sh "git checkout development"
        echo "Checking out the master branch"
        sh "git checkout master"
        echo "Merging development into master branch"
        sh "git merge development"
        echo "pushing to origin master"
        sh "git push origin master"
        echo "Tagging the associate Application Release"
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
    }
  }
}
