@Library('my-jenkins-shared') _

def generateStages(String version) {
  return {
    node {
      stage("Build ${version}") {
        echo 'test'
      }
      stage("Build 2 ${version}") {
        echo 'test'
      }
    }
  }

  // return {
  //   stage("Build ${version}") {
  //     echo 'test'
  //   }
  // }

  // return {
  //   stage("Build ${version}") {
  //     stages {
  //       stage('test') {
  //         echo 'test'
  //       }
  //     }
  //   }
  // }
}

def modules = [:]
pipeline {
  agent {
    label 'master' // test-1
  }

  environment {
    NODE_VERSIONS = "10 12 13 14 15 16"
    NODE_VERSION_DEFAULT = "14"
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
        nvm.runSh('npm init', env.NODE_VERSION_DEFAULT)
      }
    }

    stage('Code ...') {
      steps {
        script {
          def parallelStagesMap = env.NODE_VERSIONS.split(' ').collectEntries {
            ["${it}" : generateStages(it)]
          }

          parallel parallelStagesMap
        }
      }
    }

    // stage('Code Analysis') {
    //   steps {
    //     script {
    //       sh """
    //         . ~/.bashrc > /dev/null;
    //         set -ex;
    //         for version in ${NODE_VERSIONS}; do \\
    //           nvm use \$version; \\
    //           npm i; \\
    //           npm run ca; \\
    //         done
    //         """
    //     }
    //   }
    // }

    // stage('Code UnitTests') {
    //   steps {
    //     script {
    //       sh """
    //         . ~/.bashrc > /dev/null;
    //         set -ex;
    //         for version in ${NODE_VERSIONS}; do \\
    //           nvm use \$version; \\
    //           npm run test; \\
    //         done
    //         """
    //     }
    //   }
    // }

    // // stage('Code Build') {
    // //   steps {
    // //     script {
    // //       sh """
    // //         . ~/.bashrc > /dev/null;
    // //         set -ex;
    // //         for version in ${NODE_VERSIONS}; do \\
    // //           nvm use \$version; \\
    // //           npm run build; \\
    // //         done
    // //         """
    // //     }
    // //   }
    // // }

    // stage('Code Docs') {
    //   steps {
    //     script {
    //       sh """
    //         . ~/.bashrc > /dev/null;
    //         set -ex;
    //         nvm use ${NODE_VERSION_DEFAULT}; \\
    //         npm run docs;
    //         """
    //     }
    //   }
    // }

    // // stage('SonarCloud ') {
    // //   steps {
    // //     script {
    // //       withCredentials([
    // //         string(credentialsId: 'sonar_server_host', variable: 'SONAR_HOST'),
    // //         string(credentialsId: 'sonar_server_login', variable: 'SONAR_LOGIN')
    // //       ]) {
    // //         sh """
    // //           . ~/.bashrc > /dev/null;
    // //           set -ex;
    // //           nvm use ${NODE_VERSION_DEFAULT}; \\
    // //           npm run sonar -- -Dsonar.host.url=${SONAR_HOST} -Dsonar.login=${SONAR_LOGIN};
    // //           """
    // //       }
    // //     }
    // //   }
    // // }
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
