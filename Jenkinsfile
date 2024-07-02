pipeline {
    agent any
    
    environment {
        DOCKER_HUB_REPO = "your-dockerhub-username/hello-world-flask"
        DOCKER_HUB_CRED = credentials('docker-hub-credentials')
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
                    docker.build("${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}")
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image("${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}").push()
                        docker.image("${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}").push("latest")
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                // 여기에 배포 단계를 추가할 수 있습니다.
                // 예: Kubernetes에 배포하거나 Docker Swarm에 서비스로 배포
                echo "Deploying the application..."
            }
        }
    }
    
    post {
        always {
            // 클린업 작업
            sh "docker rmi ${DOCKER_HUB_REPO}:${env.BUILD_NUMBER}"
        }
    }
}
