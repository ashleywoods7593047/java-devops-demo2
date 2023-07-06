pipeline{
    agent any
    
    environment {
      hello = "123456"
      world = "456789"
    }
    
    stages{
        
        stage('检查基本环境'){
            steps{
                echo '正在检查基本信息'
                sh 'java -version'
                sh 'git --version'
                sh 'mvn -v'
                sh 'docker version'
            }
        }
        stage('编译'){
            steps{
               echo "编译..........."
               echo "${hello}"
               sh 'pwd && ls -alh'
               sh 'printenv'
            }
        }
        stage('测试'){
                steps{
                   echo "测试........."
                   echo "${world}"
                }
        }
        stage('打包'){
                steps{
                           echo "打包........"
                        }
        }
        stage('部署'){
                steps{
                           echo "部署.............."
                        }
        }
    
    }
}