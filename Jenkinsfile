pipeline {
  agent any
  environment {
        REGISTRY = '128532453810.dkr.ecr.ap-southeast-1.amazonaws.com'
        APPS = 'cilist-frontend'
  }
    stages{
      stage('Edit ENV') {
        steps {
              sh "echo REACT_APP_BACKEND=${URL_STAGING}:5000 > .env"
          }
        }
      }
      stage('Build with Docker') {
        steps {
          sh "docker build -f Dockerfile -t ${REGISTRY}/${APPS}:main-${BUILD_NUMBER} -t ${REGISTRY}/${APPS}:latest ."
        }
      }
    }
    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir()
        }
        success {
            echo 'I succeeded!'
        }
        failure {
            echo 'I failed :('
        }
    }
}
