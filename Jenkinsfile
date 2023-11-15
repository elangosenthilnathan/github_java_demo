#!groovy

CODE_CHANGES = getGitChanges()

pipeline {
  
  agent any
  environment {
    SHIFTLEFT_ACCESS_TOKEN = credentials('SHIFTLEFT_ACCESS_TOKEN')
  }
  options{
      skipDefaultCheckout()
      disableConcurrentBuilds()
  }  
  triggers {
      pollSCM('0 8 * * 0')
  }
  stages {
    stage('cleanUp') {
        steps {
            script {
                try {
                    deleteDir()
                } catch (err) {
                    println("WARNING: Failed to delete directory: " + err)
                }
            }
        }
    }    
    stage("init") {
      steps {
          script {
            curl https://cdn.shiftleft.io/download/sl > /tmp/sl && chmod a+rx /tmp/sl
          }
      }
    }
    stage("build") {
        steps {
          script {
            mvn clean build
          }
        }
    stage("QWiet NextGen Scanning") {
        steps {
          script {
            /tmp/sl analyze --app HelloShiftLeft10 --javasrc --tag branch=$BRANCH_NAME .
          }
        }    
    stage("Check-Analysis") {
        when {
          expression {
            CODE_CHANGES == true
          }
        }
        steps {
          script {
            /tmp/sl check-analysis --config qwietbuildrules.yml --app HelloShiftLeft10 
          }
        }           
    }
  }
