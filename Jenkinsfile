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
                    docker.withRegistry('https://index.docker.io/v1/', 'docker') {
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
                // Add actual deployment steps here
            }
        }

        stage('Push to GitHub') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'git', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    script {
                        def encodedPassword = URLEncoder.encode("$GIT_PASSWORD", "UTF-8")
                        sh """
                            git config user.email "stradivirus9@gmail.com"
                            git config user.name "stradivirus"
                            git add .
                            git diff --quiet && git diff --staged --quiet || git commit -m "Build successful: ${env.BUILD_NUMBER}"
                            git push https://${GIT_USERNAME}:${encodedPassword}@github.com/Stradivirus/hello.git HEAD:master
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}