// https://jenkins.io/doc/book/pipeline/syntax/

def get_choices(values) {
  if (values == "") {
    return ""
  }

  def result = ''
  def choices = values.split(',')
  if (choices.length == 0) {
    return ""
  }

  for (c in choices) {
    result += "${c}\n"
  }
  return result[0..-3]  // 最后个换行不要
}

def TEMPLATE_NOS = get_choices("${TEMPLATE_NOS}")

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
        booleanParam(name: 'REFRESH', defaultValue: false, description: '刷新: 当更改了全局变量的值, 需要先勾选此项进行配置的刷新')
        choice(name: 'TEMPLATE_NO', choices: TEMPLATE_NOS, description: '序列号: build 用到的序列号, 不同环境会不同, 只有前端用到此参数')
    }

    stages {
      // 检出代码
      stage('Checkout') {
        steps {
          script {
            if (params.REFRESH) {
              echo "成功刷新了流水线配置, 退出流水线"
              return
            }            
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