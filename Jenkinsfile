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
        stage ('Build') {
            steps {
            sh 'mvn clean package'
            }
        }

        stage ('Deploy-To-Tomcat') {
            steps {
                sshagent(['tomcat']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@18.197.52.92:/home/ubuntu/prod/apache-tomcat-10.1.28/webapps/webapp.war'
                }
            }
        }
        
    }
}

  
