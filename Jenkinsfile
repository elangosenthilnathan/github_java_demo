CODE_CHANGES = getGitChanges()

pipeline {
  
  agent any
  environment {
    SHIFTLEFT_ACCESS_TOKEN = credentials('SHIFTLEFT_ACCESS_TOKEN')
  }

  stages {
    stage("init") {
        curl https://cdn.shiftleft.io/download/sl > /tmp/sl && chmod a+rx /tmp/sl
    }
    stage("build") {
        steps {
            mvn clean build
        }
    stage("QWiet NextGen Scanning") {
        steps {
            sh '/tmp/sl analyze --app HelloShiftLeft10 --javasrc --tag branch=$BRANCH_NAME .'
        }    
    stage("Check-Analysis") {
        when {
          expression {
            CODE_CHANGES == true
          }
        }
        steps {
          sh '/tmp/sl check-analysis --config qwietbuildrules.yml --app HelloShiftLeft10 '
        }           
    }
  }
