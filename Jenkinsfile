pipeline {
    agent any

    environment {
        DOCKER_HUB_CRED = credentials('docker-hub-credentials')
    }

    stages {
        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh 'echo $DOCKER_HUB_CRED_PSW | docker login -u $DOCKER_HUB_CRED_USR --password-stdin'
                    def imageName = "your-dockerhub-username/your-image-name:${env.BUILD_NUMBER}"
                    sh "docker build -t ${imageName} ."
                    sh "docker push ${imageName}"
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}