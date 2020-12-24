Jenkins

Jenkins配置LDAP，因为jenkins用户和LDAP用户不能共存，所以配置前需要备份配置文件(config.xml)，防止配置出错。

#### pipline

```java
def javaBuild(serviceName,serviceGitUrl){
        dir(path: "${serviceName}/"){
            git branch: "${params.branch}", credentialsId: '161395f3-9b0d-46e6-ac13-f4a7dfe89ba3', url: "${serviceGitUrl}"
            sh "pwd"
            sh "mvn clean &&  mvn install && mvn deploy -Dmaven.test.skip=true -U"
            sh "echo -e  'FROM registry.cn-hongkong.aliyuncs.com/fy_server/jdk:1.8.0_202\nADD target/${serviceName}.jar /app/' > dockerfile "
            sh "docker build -t registry.cn-hongkong.aliyuncs.com/fy_server/${serviceName}:${env.ImageTag} ."
            sh "docker push registry.cn-hongkong.aliyuncs.com/fy_server/${serviceName}:${env.ImageTag} &&  docker rmi -f registry.cn-hongkong.aliyuncs.com/fy_server/${serviceName}:${env.ImageTag}"
        }
}

pipeline{
    agent any
    tools {
      jdk 'jdk'
      maven 'maven'
    }
    environment {
        fy_framework_auth='http://10.10.95.175/fy-framework/fy-framework-auth.git'
    }
    parameters {
        string(name: 'branch', defaultValue: 'master', description: '分支名称')
        string(name: 'ImageTag', defaultValue: '', description: '镜像id')
        booleanParam defaultValue: false, description: '', name: 'fy_framework_auth'
    }
    stages {
        stage("deleteDir") {
			steps{
				script{
					sh("ls -al ${env.WORKSPACE}")
					deleteDir()
					sh("ls -al ${env.WORKSPACE}")
				}
			}
		}
        stage('imageTag')
        {
            steps
            {
                script{
                    if(!"${env.ImageTag}"){
                        env.ImageTag= new Date().format("yyyMMddHHmmss") 
                    }
                    echo "${env.ImageTag}"
                    sh "mkdir fy-framework-auth"
                }
            }
        }
        stage("build"){
            parallel {
                stage('fy_framework_auth'){
                    when{
                        expression {
                          return params.fy_framework_auth
                        }
                    }
                    steps {
                        script{
                            javaBuild("fy-framework-auth","${env.fy_framework_auth}")
                        }
                        
                    }
                }
                stage('fy_service_robot'){
                    when{
                        expression {
                          return params.fy_service_robot
                        }
                    }
                    steps {
                        script{
                            javaBuild("fy-service-robot","${env.fy_service_robot}")
                        }
                        
                    }
                }
            }
        }
    }
}
```

