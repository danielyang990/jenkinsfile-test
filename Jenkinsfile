// https://jenkins.io/doc/book/pipeline/syntax/

def BRANCH_NAMES = "develop\nmaster"
def GIT_COMMIT
def GIT_BRANCH
def ENVIRONMENT  // 要部署到哪个环境

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
        choice(name: 'GIT_BRANCH', choices: BRANCH_NAMES, description: '构建的分支名称')
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
            GIT_BRANCH = params.BRANCH_NAME
            ENVIRONMENT = params.ENVIRONMENT

            echo "开始 checkout 代码"
            def scmVars
            retry(2) {
                // scmVars = git branch: "${GIT_BRANCH}", url: "${env.CODE_REPOSITORY}"
                scmVars = checkout scm
            }

            // 提取 git 信息
            env.GIT_COMMIT = scmVars.GIT_COMMIT
            env.GIT_BRANCH = scmVars.GIT_BRANCH
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