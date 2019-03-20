pipeline {
  agent none
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
  stages {
    stage('Test') {
            agent {label 'nodejs-app'}
      steps {
        checkout scm
        container('nodejs'){
        echo 'Hello World!'   
        sh 'node --version'
        }
      }
      post {
        success {
          stash name: 'app', includes: '*.js, public/**, views/*, Dockerfile'
        }
      }
    }
   
  stage('Build and Push Image') {
      steps {
      
        echo "TODO - build and push image"
        sh 'maven --version'
        unstash 'app'
        }
      }
      post {
        success {
          slackSend message: "${JOB_NAME} pipeline job is awaiting approval at: ${RUN_DISPLAY_URL}"
        }
      }
    }
   
  stage('Deploy') {
      options {
        timeout(time: 60, unit: 'SECONDS')
      }
 
      input {
        message "Should we continue?"
        submitter "tjohnson"
        submitterParameter "APPROVER"
      }
      steps {
        echo "Continuing with deployment - approved by ${APPROVER}"
      }
   }
  }  
}