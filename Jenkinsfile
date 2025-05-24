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
    image: jenkins/inbound-agent:latest
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
// This Jenkinsfile uses a Kubernetes agent to run a Helm deployment.
// It defines a pod with two containers: one for Helm and one for the Jenkins agent.