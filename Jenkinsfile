pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'stradivirus/hello'
        DOCKER_CREDENTIALS = credentials('docker')
        GITHUB_CREDENTIALS = credentials('git')
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
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS) {
                        def dockerImage = docker.image("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                // 실제 배포 단계를 여기에 추가하세요
            }
        }
    }
}
