
pipeline {
    agent any

    tools {
        jdk 'JDK-25'
        maven 'Maven-3.9'
    }

    environment {
        DOCKER_IMAGE = 'adison1/user-service'
        DOCKER_HOST = 'tcp://docker:2376'
        DOCKER_TLS_VERIFY = '1'
        DOCKER_CERT_PATH = '/certs/client'
    }

    stages {
        stage('Verify Tools') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
                sh 'docker version'
            }
        }

        stage('Build and Test') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} -t ${DOCKER_IMAGE}:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-adison1',
                        usernameVariable: 'DOCKERHUB_USERNAME',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        set +x
                        echo "$DOCKERHUB_TOKEN" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                        docker push "$DOCKER_IMAGE:$BUILD_NUMBER"
                        docker push "$DOCKER_IMAGE:latest"
                        docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'User service built and published successfully!'
        }
        failure {
            echo 'Build or publishing failed. Check the console output.'
        }
        always {
            archiveArtifacts artifacts: 'target/*.jar',
                             allowEmptyArchive: true
            sh 'docker logout || true'
        }
    }
}
