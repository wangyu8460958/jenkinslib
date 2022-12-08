#!groovy

@Library('jenkinslib') _ 

def tools = new org.devops.tools()

String  workspace = "/var/jenkins_home/workspace"


pipeline {
    agent { node { label "jenkins-slave"   //指定运行节点的标签或名称
                    customWorkspace "${workspace}"    //指定运行工作目录(可选)
        }        
    }
    
    options {
        timestamps()   //日志会有时间 （排查问题的时候查看什么时候失败的，会在日志里面打出时间。）
        skipDefaultCheckout()   //删除隐式checkout scm语句  （声明式的脚本会默认的检查你有没有配置代码库，如果有的话它会自动的下载，但是我们没有必要下载，所以我们这里跳过）
        disableConcurrentBuilds()  //禁止并行
        timeout(time: 1, unit: 'HOURS')  //流水线超时设置1h
    }
    
    stages {
        
        //  下载代码
        stage("GetCode"){ //阶段名称
            when { environment name: 'test', value: 'abcd' }
            steps{   //步骤
                timeout(time:5, unit:"MINUTES"){  //步骤超时时间
                    script{  //填写运行代码
                        println('获取代码')
                        tools.PrintMes("获取代码", 'red')
                        println("${test}")

                        input id: 'Test', message: '我们是否要继续? ', ok: '是, 继续吧! ', parameters: [choice(choices: ['a', 'b'], description: '', name: 'test1')], submitter: 'lizeyang,admin'
                    }
                }
            }
        }
        
        stage("01"){
            failFast true
            parallel {
                
                 stage("Build"){
                    steps{
                        timeout(time:20, unit:"MINUTES"){
                            script{  
                                println('应用打包')
                                tools.PrintMes("应用打包", 'blue')
                                mvnHome = tool "m2"
                                println(mvnHome)
                                
                                sh "${mvnHome}/bin/mvn --version"
                            }
                        }
                    }
                 }
                 
        
          //代码扫描
                 stage("CodeScan"){
                    steps{
                        timeout(time:30, unit:"MINUTES"){
                            script{  
                                println('代码扫描')
                                tools.PrintMes("代码扫描", 'green')
                            }
                        }
                    }
                 }  
            }
        }
    }
    
post {
       always  {
             script {
                 println("always")
             }
        }




      success {
             script {
                 currentBuild.description += "\n 构建成功!"
             }
      }




      failure {
             script {
                 currentBuild.description += "\n 构建失败!"
             }
      }




      aborted {
             script {
                 currentBuild.description += "\n 构建取消!"
             }
      }
   }
}
