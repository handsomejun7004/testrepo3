pipeline {
    agent any

    environment {
        ECR_PATH = '076508105214.dkr.ecr.ap-northeast-2.amazonaws.com'
        ECR_IMAGE = 'bastion'
        REGION = 'ap-northeast-2'
        ACCOUNT_ID='076508105214'
    }

    stages {
        stage('Git Clone from gitSCM') {
            steps {
                script {
                    try {
                        git branch: 'main',
                            credentialsId: '84f16736-af9c-447b-9251-996f3ee43b0a',
                            url: 'https://github.com/handsomejun7004/testrepo'
                        sh "ls -lat"
                        pwd
                        env.cloneResult=true
                    } catch (error) {
                        print(error)
                    }
                }
            }
        }
        stage('Docker Build'){
            steps {
                script{
                    try {
                        sh """
                        #!/bin/bash
                        cat>Dockerfile<<-EOF
FROM skawk7004/junhoimage:v1
ADD server.xml /usr/local/tomcat/conf/server.xml
ADD redis-data-cache.properties /usr/local/tomcat/conf/redis-data-cache.properties
ADD index.jsp /usr/local/tomcat/webapps/ROOT/index.jsp
ADD db.jsp /usr/local/tomcat/webapps/ROOT/db.jsp
ADD session.jsp /usr/local/tomcat/webapps/ROOT/session.jsp
WORKDIR /usr/local/tomcat/bin
CMD ["./catalina.sh", "run"]
EOF
"""
                } catch (error) {
                        print(error)
                    }
            }
        }
        }
        stage('Push to ECR'){
            steps {
              script {
                docker.withRegistry("https://${ECR_PATH}", "ecr:ap-northeast-2:aws_token") {
                    def image = docker.build("${ECR_PATH}/${ECR_IMAGE}:${env.BUILD_NUMBER}")
                        image.push()
                }

                echo 'Remove Deploy Files'
                sh "rm -rf /var/lib/jenkins/workspace/${env.JOB_NAME}/*"
                env.dockerBuildResult=true
              }
            }
        }
    }
}
