pipeline {
    agent any 
    tools {
        maven 'Maven'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                echo "PATH = ${PATH}"
                echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }

        stage ('Check-Git-Secrets') {
          steps {
            sh 'rm trufflehogoutput || true'
            sh 'docker run trufflesecurity/trufflehog --json git https://github.com/cybermanish2023/DevSecOpsWebapp.git > trufflehogoutput '
            sh 'cat trufflehogoutput'
          }
        }

        stage ('Source Composition Analysis') {
            steps {
                sh 'rm owasp* || true'
                sh 'wget https://github.com/cybermanish2023/DevSecOpsWebapp/blob/main/owasp-dependency-check.sh'
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh'
            }
        }
        
        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('Deploy-To-Tomcat') {
            steps {
                sshagent(['tomcat']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.201.101.252:/home/ubuntu/prod/apache-tomcat-9.0.93/webapps/webapp.war'
                }
            }
        }
    }
}
