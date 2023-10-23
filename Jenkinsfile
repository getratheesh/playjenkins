pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            // Assuming that you have stored the Harbor registry credentials in Jenkins
            // Replace 'HARBOR_CREDENTIALS' with your actual credentials ID from Jenkins
            withCredentials([usernamePassword(credentialsId: 'HARBOR_CREDENTIALS', usernameVariable: 'HARBOR_USERNAME', passwordVariable: 'HARBOR_PASSWORD')]) {
                sh '''
                /kaniko/executor --dockerfile `pwd`/Dockerfile \
                                --context `pwd` \
                                --destination=harbor.uls.uled.io/uled/myweb:${BUILD_NUMBER}
                '''
            }
          }
        }
      }
    }

    stage('Deploy App to Kubernetes') {     
      steps {
        container('kubectl') {
          withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" myweb.yaml'
            sh 'kubectl apply -f myweb.yaml'
          }
        }
      }
    }
  
  }
}
