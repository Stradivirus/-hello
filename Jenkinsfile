pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'stradivirus/hello'
        DOCKER_CREDENTIALS = credentials('docker-hub-credentials')
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
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push()
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}").push("latest")
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
            }
        }
    }

    post {
        always {
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
        }
    }
}