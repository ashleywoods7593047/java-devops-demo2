pipeline{
    agent any
    
    environment {
      hello = "123456"
      world = "456789"
      ws="${WORKSPACE}"
      ALIYUN_SECRTE=credentials("aliyun-docker-repo")
      IMAGE_VERSION="v1.0"
    }
    
    stages{
        
        stage('检查基本环境'){
            steps{
                echo '正在检查基本信息'
                sh 'java -version'
                sh 'git --version'
               
                sh 'docker version'
                echo '正在提交信息'
            }
        }
        stage('编译'){
            agent {
                docker {
                image 'maven:3-alpine'
                args  '-v /var/jenkins_home/appconfig/maven/.m2:/root/.m2'
                }
                
              }
            steps{
               echo "编译..........."
               echo "${hello}"
               sh 'pwd && ls -alh'
               sh 'printenv'
               sh 'mvn -v'
               //打包
               sh 'cd ${ws} && mvn clean package  -s "/var/jenkins_home/appconfig/maven/settings.xml" -Dmaven.test.skip=true'
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
                    sh 'docker version' 
                    sh 'pwd && ls -alh'
                    sh 'docker build  -t java-devops-demo .'    
                }
        }
        stage('部署'){
                steps{
                    echo "部署.............."
                    sh 'docker stop java-devops-demo-dev'
                    sh 'docker rm java-devops-demo-dev'
                    sh 'docker run -d -p 8888:8080 --name=java-devops-demo-dev java-devops-demo'          
                 }
                 
                 post {
                   failure {
                     echo "炸了"
                   }
                   success{
                     echo "成功了"
                   }
                 }
        }
        
        stage('推送镜像'){
           steps{
              sh "docker login -u=${ALIYUN_SECRTE_USR} -p=${ALIYUN_SECRTE_PSW} registry.cn-hangzhou.aliyuncs.com"
              sh "docker tag java-devops-demo registry.cn-hangzhou.aliyuncs.com/ranjingnian_dev/java-devops-demo:${IMAGE_VERSION}"
              sh "docker push registry.cn-hangzhou.aliyuncs.com/ranjingnian_dev/java-devops-demo:${IMAGE_VERSION}"
           }
        }
        
        stage('发送报告'){
            steps {
                echo '准备发送报告'
                emailext body: '''<!DOCTYPE html>    
                <html>    
                <head>    
                <meta charset="UTF-8">    
                <title>${ENV, var="JOB_NAME"}-第${BUILD_NUMBER}次构建日志</title>    
                </head>    
                    
                <body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"    
                    offset="0">    
                    <table width="95%" cellpadding="0" cellspacing="0"  style="font-size: 11pt; font-family: Tahoma, Arial, Helvetica, sans-serif">    
                        <tr>    
                            本邮件由系统自动发出，无需回复！<br/>            
                            各位同事，大家好，以下为${PROJECT_NAME }项目构建信息</br> 
                            <td><font color="#CC0000">构建结果 - ${BUILD_STATUS}</font></td>   
                        </tr>    
                        <tr>    
                            <td><br />    
                            <b><font color="#0B610B">构建信息</font></b>    
                            <hr size="2" width="100%" align="center" /></td>    
                        </tr>    
                        <tr>    
                            <td>    
                                <ul>    
                                    <li>项目名称 ： ${PROJECT_NAME}</li>    
                                    <li>构建编号 ： 第${BUILD_NUMBER}次构建</li>    
                                    <li>触发原因： ${CAUSE}</li>    
                                    <li>构建状态： ${BUILD_STATUS}</li>    
                                    <li>构建日志： <a href="${BUILD_URL}console">${BUILD_URL}console</a></li>    
                                    <li>构建  Url ： <a href="${BUILD_URL}">${BUILD_URL}</a></li>    
                                    <li>工作目录 ： <a href="${PROJECT_URL}ws">${PROJECT_URL}ws</a></li>    
                                    <li>项目  Url ： <a href="${PROJECT_URL}">${PROJECT_URL}</a></li>    
                                     <li>测试报告： <a href="${PROJECT_URL}allure">${PROJECT_URL}allure</a></li> 
                                </ul>    
                
                <h4><font color="#0B610B">失败用例</font></h4>
                <hr size="2" width="100%" />
                $FAILED_TESTS<br/>
                
                <h4><font color="#0B610B">最近提交(#$SVN_REVISION)</font></h4>
                <hr size="2" width="100%" />
                <ul>
                ${CHANGES_SINCE_LAST_SUCCESS, reverse=true, format="%c", changesFormat="<li>%d [%a] %m</li>"}
                </ul>
                详细提交: <a href="${PROJECT_URL}changes">${PROJECT_URL}changes</a><br/>
                
                            </td>    
                        </tr>    
                    </table>    
                </body>
                ''', subject: '${ENV, var="JOB_NAME"}-第${BUILD_NUMBER}次构建日志', to: '16623135334@163.com'
            }
        }
        
        
        stage('部署到生产环境'){
            steps{
                input message: '需要部署到生产环境吗？', ok: '需要', parameters: [text(defaultValue: 'v1.0', description: '生产环境需要部署的版本', name: 'IMAGE_VERSION')]
            }
        }
    }
}