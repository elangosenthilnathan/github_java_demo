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
      pollSCM('0 9 * * 0')
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

    stage("Download Qwiet") {
      steps {
        script {
            sh """curl https://cdn.shiftleft.io/download/sl > ./sl && chmod a+rx ./sl"""
          }
        }
    } 
 
    stage("Qwiet NextGen Scanning") {
        steps {
          script {
            sh """./sl analyze --wait --app HelloShiftLeft10 --javasrc . """
          }
        }
    }    
  }
}
