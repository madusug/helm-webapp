pipeline {
    agent {
        docker {
            image 'alpine/helm:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    stages {
        stage('Deploy with Helm') {
            steps {
                sh 'helm upgrade --install my-webapp ./webapp1 --namespace default'
            }
        }
    }
}