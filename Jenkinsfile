pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: helm-deploy
spec:
  containers:
  - name: helm
    image: alpine/helm:latest
    command:
    - cat
    tty: true
"""
        }
    }
    stages {
        stage('Deploy with Helm') {
            steps {
                container('helm') {
                    sh 'helm upgrade --install my-webapp ./webapp1 --namespace default'
                }
            }
        }
    }
}