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
                sh 'rm trufflehog || true'
                sh 'docker run gesellix/trufflehog --json https://github.com/cybermanish2023/DevSecOpsWebapp.git > trufflehog'
                sh 'cat trufflehog'
            }
        }

        stage ('Source Composition Analysis') {
            steps {
                sh 'rm owasp* || true'
                sh 'wget "https://raw.githubusercontent.com/cybermanish2023/DevSecOpsWebapp/main/owasp-dependency-check.sh"'
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh --nvdApiKey aa67805d-fdc7-4072-994d-a5d7ce67ed96'
            }
        }  

        stage ('SAST') {
            steps {
                sh '''
                echo "Pulling Semgrep Docker Image from Docker Hub"
                docker pull returntocorp/semgrep
                
                echo "Starting Semgrep scan"
                docker run --rm -v "${PWD}:/src" returntocorp/semgrep semgrep --config auto /src
                echo "Semgrep scan completed"
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

        stage ('DAST') {
            steps {
                sh '''
                echo "Pulling OWASP ZAP Docker Image from Docker Hub"
                docker pull zaproxy/zap-stable
                
                echo "Starting OWASP ZAP DAST scan"
                docker run -u root -v ${WORKSPACE}:/zap/wrk:rw -t zaproxy/zap-stable zap-baseline.py -t http://18.197.52.92:8080/webapp/ -r zap_report.html
                echo "ZAP DAST scan completed"
                '''
            }
        }

        stage ('Archive Report') {
            steps {
                sh 'ls -la $(pwd)'
                archiveArtifacts artifacts: 'zap_report.html', fingerprint: true
            }
        }
    }
}
