#!groovy

pipeline {
  
  agent { docker { image 'shiftleft/core:latest' } }

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
    stage("Update Qwiet") {
        steps {
          script {
            sh """mkdir app && curl 'https://cdn.shiftleft.io/download/sl' > app/sl"""
          }
        }
    }      
    stage("Qwiet NextGen Scanning") {
        steps {
          script {
            sh """app/sl analyze --wait --app HelloShiftLeft10 --javasrc . """
          }
        }
    }    
    stage("Check-Analysis") {
        steps {
          script {
            sh """app/sl check-analysis --config shiftleft.yml --app HelloShiftLeft10 """
          }
        }           
    }
  }
}
