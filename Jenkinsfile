#!groovy

pipeline {
  
  agent any
  environment {
    SHIFTLEFT_ACCESS_TOKEN = credentials('SHIFTLEFT_ACCESS_TOKEN')
    FULL_PATH_BRANCH = "${sh(script:'git name-rev --name-only HEAD', returnStdout: true)}" 
    GIT_BRANCH = FULL_PATH_BRANCH.substring(FULL_PATH_BRANCH.lastIndexOf('/') + 1, FULL_PATH_BRANCH.length())    
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
    stage('checkout') {
      steps {
        git(
        url: 'https://github.com/elangosenthilnathan/github_java_demo.git',
        credentialsId: 'github_user',
        branch: "main"
        )
      }
    }
    stage("init") {
      steps {
          script {
            sh """curl https://cdn.shiftleft.io/download/sl > /tmp/sl && chmod a+rx /tmp/sl"""
          }
      }
    }
    stage("build") {
        steps {
          script {
            sh """mvn compile package"""
          }
        }
    }
    stage("Qwiet NextGen Scanning") {
        steps {
          script {
            sh """/tmp/sl analyze --wait --app HelloShiftLeft10  --tag branch=${GIT_BRANCH} --java target/*.jar"""
          }
        }
    }    
    stage("Check-Analysis") {
        steps {
          script {
            sh """/tmp/sl check-analysis --config shiftleft.yml --app HelloShiftLeft10 """
          }
        }           
    }
  }
}
