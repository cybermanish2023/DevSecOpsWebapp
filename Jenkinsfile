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

        stage ('Source Composition Analysis') {
            steps {
                script {
                    // Remove any previous dependency check files
                    sh 'rm -f owasp*'
                    // Download the OWASP Dependency-Check script
                    sh 'wget https://raw.githubusercontent.com/cybermanish2023/DevSecOpsWebapp/main/owasp-dependency-check1.sh -O owasp-dependency-check1.sh'
                    // Make the script executable
                    sh 'chmod +x owasp-dependency-check1.sh'
                    // Execute the script
                    sh './owasp-dependency-check1.sh'
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
}
