@Library('my-jenkins-shared') _

def modules = [:]
pipeline {
  agent {
    label 'master' // test-1
  }

  environment {
    // NODE_VERSIONS = "10 12 13 14 15 16"
    NODE_VERSION_DEFAULT = "14"
    RUN_SONAR_SCANNER = 0
  }

  parameters {
    string(
      defaultValue: '14',
      description: 'Node.js version to run tests for',
      name: 'NODE_VERSION',
      trim: true
    )
  }

  stages {
    stage('Info') {
      steps {
        script {
          nvm.info()
        }
      }
    }
    stage('Init') {
      steps {
        script {
          nvm.runSh 'npm i', params.NODE_VERSION
        }
      }
    }
    stage("Code Analysis") {
      steps {
        script {
          nvm.runSh "npm run ca", params.NODE_VERSION
        }
      }
    }
    // stage("Code Sonar") {
    //   steps {
    //     when {
    //       anyOf {
    //         env.RUN_SONAR_SCANNER 1
    //       }
    //     }
    //     script {
    //       if (params.NODE_VERSION == env.NODE_VERSION_DEFAULT) {
    //         withCredentials([
    //           string(credentialsId: 'sonar_server_host', variable: 'SONAR_HOST'),
    //           string(credentialsId: 'sonar_server_login', variable: 'SONAR_LOGIN')
    //         ]) {
    //           nvm.runSh "npm run sonar -- -Dsonar.host.url=${SONAR_HOST} -Dsonar.login=${SONAR_LOGIN}", params.NODE_VERSION
    //         }
    //       } else {
    //         echo "skip"
    //       }
    //     }
    //   }
    // }
    stage("Code UnitTest") {
      steps {
        script {
          nvm.runSh "npm run test", params.NODE_VERSION
        }
      }
    }
    stage("Code Docs") {
      steps {
        script {
          if (params.NODE_VERSION == env.NODE_VERSION_DEFAULT) {
            nvm.runSh "npm run docs", params.NODE_VERSION
          } else {
            echo "skipped"
          }
        }
      }
    }
    stage("Code Build") {
      steps {
        script {
          nvm.runSh "npm run build", params.NODE_VERSION
        }
      }
    }
  }
  post {
    // https://www.jenkins.io/doc/pipeline/tour/post/
    // https://plugins.jenkins.io/telegram-notifications/
    failure {
      script {
        telegram.sendStatusFail('jk_pipeline_report_to_telegram_token','jk_pipeline_report_to_telegram_chatId')
      }
    }
    success {
      script {
        telegram.sendStatusOk('jk_pipeline_report_to_telegram_token','jk_pipeline_report_to_telegram_chatId')
      }
    }
  }
}
