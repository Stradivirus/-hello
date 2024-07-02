environment {
    DOCKER_HUB_CRED = credentials('docker-hub-credentials')
}

stages {
    stage('Docker Login') {
        steps {
            sh 'echo $DOCKER_HUB_CRED_PSW | docker login -u $DOCKER_HUB_CRED_USR --password-stdin'
        }
    }
}