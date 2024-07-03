pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'stradivirus/hello'
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
                // 여기에 배포 스크립트를 추가하세요. 예:
                // sh "docker run -d -p 5000:5000 ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                echo "Deploying ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
            }
        }
    }

    post {
        always {
            // 정리 작업
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
        }
    }
}