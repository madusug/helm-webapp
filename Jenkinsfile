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
  serviceAccountName: jenkins-sa
  containers:
  - name: helm
    image: alpine/helm:latest
    command:
    - cat
    tty: true
  - name: jnlp
    image: jenkins/inbound-agent:3309.v27b_9314fd1a_4-1
    resources:
      requests:
        memory: "256Mi"
        cpu: "100m"
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