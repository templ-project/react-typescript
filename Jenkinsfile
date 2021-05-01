@Library('my-jenkins-shared') _

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

    // stage('Init') {
    //   steps {
    //     script {
    //       nvm.runSh 'npm i', env.NODE_VERSION_DEFAULT
    //     }
    //   }
    // }

    // stage('Code ...') {
    //   steps {
    //     script {
    //       echo "freaking workspace: ${env.WORKSPACE}"

    //       def parallelStagesMap = generateParallelSagesMap(env.NODE_VERSIONS, env.WORKSPACE)

    //       parallel parallelStagesMap
    //     }
    //   }
    // }

    stage('Code ...') {
      steps {
        stages {
          ("A".."F").collect {
            stage("Build ${it}") {
              agent any
              steps {
                echo "Hello from ${it}.x"
                sh "pwd; ls -la"
              }
            }
          }
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

/**
 * @link https://gist.github.com/richid/d498346030275ae1790618039d8965c0
 * @link https://issues.jenkins.io/browse/JENKINS-58236
 */

def generateStages(String version, String wksp) {
  // return {
  //   node {
  //     stage("Code Analysis ${version}") {
  //       nvm.runSh "cd ${wksp}; npm run ca", version
  //     }
  //     stage("Code UnitTests ${version}") {
  //       nvm.runSh "cd ${wksp}; npm run test", version
  //     }
  //     stage("Code Build ${version}") {
  //       nvm.runSh "cd ${wksp}; npm run build", version
  //     }
  //     if (version == '14') {
  //       stage("Code Docs ${version}") {
  //         nvm.runSh "cd ${wksp}; npm run docs", version
  //       }
  //     }
  //     // stage("Code Sonar ${version}") {
  //     //   if (version == '14') {
  //     //     withCredentials([
  //     //       string(credentialsId: 'sonar_server_host', variable: 'SONAR_HOST'),
  //     //       string(credentialsId: 'sonar_server_login', variable: 'SONAR_LOGIN')
  //     //     ]) {
  //     //       sh """
  //     //         . ~/.bashrc > /dev/null;
  //     //         set -ex;
  //     //         nvm use ${NODE_VERSION_DEFAULT}; \\
  //     //         npm run sonar -- -Dsonar.host.url=${SONAR_HOST} -Dsonar.login=${SONAR_LOGIN};
  //     //         """
  //     //     }
  //     //   }
  //     // }
  //   }
  // }

  // return {
  //   stage("Build ${version}") {
  //     echo 'test'
  //   }
  // }

  return stage("Build ${version}") {
    steps {
      echo 'test'
    }
  }

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

def generateParallelSagesMap(String versions, String wksp) {
  return versions.split(' ').collectEntries {
    ["node-${it}" : generateStages(it, wksp)]
  }
}
