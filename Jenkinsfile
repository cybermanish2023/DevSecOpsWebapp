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
                script {
                    // Ensure any previous output file is removed
                    sh 'rm -f trufflehogoutput'
                    // Run TruffleHog and redirect output to a file
                    sh 'docker run --rm trufflesecurity/trufflehog --json git https://github.com/cybermanish2023/DevSecOpsWebapp.git > trufflehogoutput'
                    // Display the content of the output file
                    sh 'cat trufflehogoutput'
                }
            }
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
