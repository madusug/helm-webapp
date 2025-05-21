pipeline {
       agent any
       stages {
	   stage('Deploy with Helm') {
	   steps {
	   script {
	   if (isUnix()) {
		   sh '''
			   helm upgrade \
			   --install my-webapp \
			   ./webapp1 \
			   --namespace default
		   '''
	   } else {
		   sh '''
			   /c/ProgramData/chocolatey/bin/helm upgrade \
			   --install my-webapp \
			   ./webapp1 \
			   --namespace default
		   '''
	   }
	   }
      }
    }
  }
}