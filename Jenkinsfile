// https://jenkins.io/doc/book/pipeline/syntax/

def GIT_COMMIT
def GIT_BRANCH = 'develop'
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
            // 这两个参数，都是由外面传进来的，不是在 jenkinsfile 中定义
            if (params.GIT_COMMIT != "") {
              GIT_BRANCH = params.GIT_BRANCH
            }
            if (params.ENVIRONMENT != "") {
              ENVIRONMENT = params.ENVIRONMENT
            }

            echo "开始 checkout 代码"
            def scmVars
            retry(2) {
                scmVars = checkout scm
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