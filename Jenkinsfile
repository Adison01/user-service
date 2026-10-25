pipeline {
    agent any

    tools {
        jdk 'JDK-25'
        maven 'Maven-3.9'
    }

    stages {
        stage('Verify Tools') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage('Build and Test') {
            steps {
                sh 'mvn clean verify'
            }
        }
    }

    post {
        success {
            echo 'User service built and tested successfully!'
        }
        failure {
            echo 'Build or tests failed. Check the console output.'
        }
        always {
            archiveArtifacts artifacts: 'target/*.jar',
                             allowEmptyArchive: true
        }
    }
}
