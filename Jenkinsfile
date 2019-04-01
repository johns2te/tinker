library 'SHARED_LIBRARY'
pipeline {
  agent none
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
  }
  stages {
    stage('Tests') {
          parallel{
            stage('Test On Windows'){
             agent {label 'nodejs-app'}
               steps {
                checkout scm
                container('nodejs'){
                echo 'Window.... Door...'   
                sh 'node --version'
                }
               }
               post {
           success {
              stash name: 'app', includes: '*.js, public/**, views/*, Dockerfile'
           }
          } 
            }
            stage('Test On Linux'){
             agent {label 'nodejs-app'}
               steps {
                 checkout scm
                 container('nodejs'){
                 echo 'Testy mc test test'   
                 sh 'node --version'
                 }
               }
            }
      }
    }
   
  stage('Build and Push Image') {
      agent any
      steps {
        build()
        unstash 'app'
        
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