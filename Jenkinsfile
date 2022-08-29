pipeline {
  agent any
  environment {
        REGISTRY = '128532453810.dkr.ecr.ap-southeast-1.amazonaws.com'
        APPS = 'cilist-frontend'
  }
    stages{
      stage('Edit ENV') {
        steps {
          script {
            if ( env.GIT_BRANCH == 'staging' ) {
              sh "sed -i 's/APPS-URL/${URL_STAGING}/g' .env"
            }
            else if ( env.GIT_BRANCH == 'main' ) {
              sh "sed -i 's/APPS-URL/${URL_PROD}/g' .env"
            }
          }
        }
      }
      stage('Build with Docker') {
        steps {
          sh "docker build -f Dockerfile -t ${REGISTRY}/${APPS}:${GIT_BRANCH}-${BUILD_NUMBER} -t ${REGISTRY}/${APPS}:${GIT_BRANCH}-latest ."
        }
      }
      stage('Publish Docker Image') {
        steps {
          sh "docker push ${REGISTRY}/${APPS}:${GIT_BRANCH}-${BUILD_NUMBER}"
          sh "docker push ${REGISTRY}/${APPS}:${GIT_BRANCH}-latest"
        }
      }
      stage('Deploy to Kubernetes') {
        steps {
          script {
            if ( env.GIT_BRANCH == 'staging' ) {
              sh "sed -i 's/IMAGE_TAG/${GIT_BRANCH}-${BUILD_NUMBER}/g' deployment.yaml"
              sh "kubectl apply -f deployment.yaml -n staging"
            }
            else if ( env.GIT_BRANCH == 'main' ) {
              sh "sed -i 's/IMAGE_TAG/${GIT_BRANCH}-${BUILD_NUMBER}/g' deployment.yaml"
              sh "kubectl apply -f deployment.yaml -n production"
            }
          }
        }
      }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir()
        }
        success {
            echo 'I Completed & succeeded!'
        }
        failure {
            echo 'I failed :('
        }
    }
}
