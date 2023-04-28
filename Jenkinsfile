pipeline {
  agent any

  environment {
    NAME = "solar-system"
    VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
    IMAGE_REPO = "prashanth2paramaah"

  }
  
  stages {
    stage('Unit Tests') {
      steps {
        echo 'Implement unit tests if applicable.'
        echo 'This stage is a sample placeholder'
      }
    }

    stage('Build Image') {
      steps {
            sh "docker build -t ${NAME} ."
            sh "docker tag ${NAME}:latest ${IMAGE_REPO}/${NAME}:${VERSION}"
        }
      }
    stage('Push Image') {
      steps {
        script {
                withCredentials([string(credentialsId: 'docker_hub', variable: 'dockerhub_passwd')]) {
                sh 'docker login -u prashanth2paramaah -p $dockerhub_passwd'
                sh 'docker push ${IMAGE_REPO}/${NAME}:${VERSION}'
                }
        }
      }
    }



  }
}
