pipeline {
    agent {
        podman {
            image 'alpine/helm:latest'
            args '-v /var/run/podman.sock:/var/run/podman.sock'
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