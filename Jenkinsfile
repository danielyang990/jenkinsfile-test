// https://jenkins.io/doc/book/pipeline/syntax/

def GIT_COMMIT
def GIT_BRANCH
def ENVIRONMENT = 'int'  // 如果外面没传递进来值，默认只部署到 int 环境

pipeline {
    agent any

    options {
        // 保留多少流水线记录
        buildDiscarder(logRotator(numToKeepStr: '10'))
        // 不允许并行执行
        disableConcurrentBuilds()
    }

    parameters {
        booleanParam(name: 'DEBUG', defaultValue: true, description: '调试模式: 会显示更详细的日志信息')
        gitParameter branchFilter: 'origin/(.*)', defaultValue: 'develop', name: 'BRANCH', type: 'PT_BRANCH'
    }

    environment {
        CODE_REPOSITORY = "https://github.com/danielyang990/jenkinsfile-test.git"
    }

    stages {
      // 检出代码
      stage('Checkout') {
        steps {
          script {
            echo "params: ${params}"
            if (params.ENVIRONMENT != "") {
              ENVIRONMENT = params.ENVIRONMENT
            }

            def scmVars
            retry(2) {
                scmVars = checkout([$class: 'GitSCM',
                          branches: [[name: "${params.BRANCH}"]],
                          doGenerateSubmoduleConfigurations: false,
                          extensions: [],
                          gitTool: 'Default',
                          submoduleCfg: [],
                          userRemoteConfigs: [[url: "${CODE_REPOSITORY}"]]
                        ])
            }

            // 提取 git 信息
            GIT_COMMIT = "${scmVars.GIT_COMMIT}"
            GIT_BRANCH = "${scmVars.GIT_BRANCH}"

            echo "GIT_BRANCH: ${GIT_BRANCH}; GIT_COMMIT: ${GIT_COMMIT}; ENVIRONMENT: ${ENVIRONMENT}"
          }
        }
      }

      stage('Build') {
        steps {
          script {
            echo "构建代码"
            build job: '../../docker-prod-config', parameters: [
              [$class: 'StringParameterValue', name: 'CHART', value: 'admin-server'],
              [$class: 'StringParameterValue', name: 'COMPONENT', value: 'backend'],
              [$class: 'StringParameterValue', name: 'IMAGE_TAG', value: GIT_COMMIT],
              [$class: 'StringParameterValue', name: 'ENVIRONMENT', value: 'staging'],
              [$class: 'BooleanParameterValue', name: 'DEBUG', value: true],
            ], wait: false              
          }
        }
      }
    }

    // 流水线执行结束
    post {
        // 成功
        success {
            echo "太棒了, 流水线执行成功!"
        }
        // 失败
        failure {
            echo "该死, 又出错了!"
        }
        // 取消的
        aborted {
          echo "取消掉了!"
        }
    }
}
