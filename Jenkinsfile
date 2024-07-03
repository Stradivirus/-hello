pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'stradivirus/hello'
        DOCKER_CREDENTIALS = credentials('docker')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${env.BUILD_NUMBER} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${env.BUILD_NUMBER}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
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
            sh "docker rmi ${DOCKER_IMAGE}:${env.BUILD_NUMBER} || true"
        }
        success {
            emailext (
                subject: "빌드 성공: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
                body: "빌드가 성공적으로 완료되었습니다.\n\n빌드 URL: ${env.BUILD_URL}",
                to: "stradivirus9@gmail.com",
                from: "jenkins@example.com"
            )
        }
        failure {
            emailext (
                subject: "빌드 실패: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
                body: "빌드가 실패했습니다. 자세한 내용은 로그를 확인해주세요.\n\n빌드 URL: ${env.BUILD_URL}",
                to: "stradivirus9@gmail.com",
                from: "jenkins@example.com"
            )
        }
    }
}