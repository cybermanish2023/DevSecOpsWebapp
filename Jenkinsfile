pipeline {
    agent any 
    tools {
        maven 'Maven' // Make sure 'Maven' is the correct name of the Maven tool in Jenkins configuration
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
    }
}
