pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'stradivirus/hello'
        DOCKER_CREDENTIALS = credentials('docker')
        GIT_CREDENTIALS = credentials('git')
        GIT_COMMITTER_NAME = 'stradivirus'
        GIT_COMMITTER_EMAIL = 'stradivirus9@gmail.com'
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

        stage('Push to GitHub') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh """
                    git rm -rf
                    git init
                    git config user.name "${GIT_COMMITTER_NAME}"
                    git config user.email "${GIT_COMMITTER_EMAIL}"
                    git add .
                    git commit -m "Build successful: ${env.BUILD_NUMBER}"
                    git push https://${GIT_CREDENTIALS_USR}:${GIT_CREDENTIALS_PSW}@github.com/Stradivirus/hello.git HEAD:main
                """
            }
        }
    }
}