pipeline {
  agent any
  stages {
    stage('Deploy with Helm') {
      steps {
        script {
          sh '/usr/sbin/helm upgrade --install my-app ./app --namespace default'
        }
      }
    }
  }
}

