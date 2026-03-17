pipeline {
    agent any

    environment {
        DOCKER_IMAGE = '2023bcs0166'
        DOCKER_CREDS_ID = 'dockerhub-credentials'
        DOCKER_HUB_USER = 'tejasri06'
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG} -t ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    sh "docker run -d -p 8085:80 ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG}"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDS_ID, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh """
                        echo \$PASS | docker login -u \$USER --password-stdin
                        docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:${TAG}
                        docker push ${DOCKER_HUB_USER}/${DOCKER_IMAGE}:latest
                        docker logout
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
            cleanWs()
        }
        success {
            echo "SUCCESS: Image pushed"
        }
        failure {
            echo "FAILED"
        }
    }
}