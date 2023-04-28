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

    stage('Clone/Pull Repo') {
      steps {
        script {
          if (fileExists('gitops-argocd')) {

            echo 'Cloned repo already exists - Pulling latest changes'

            dir("gitops-argocd") {
              sh 'git pull'
            }

          } else {
            echo 'Repo does not exists - Cloning the repo'
            sh 'git clone -b feature1 https://github.com/Prashanth2Paramaah/gitops-argocd.git'
          }
        }
      }
    }
     stage('Update Manifest') {
      steps {
        dir("gitops-argocd/jenkins-demo") {
          sh 'sed -i "s#prashanth2paramaah.*#${IMAGE_REPO}/${NAME}:${VERSION}#g" deployment.yaml'
          sh 'cat deployment.yaml'
        }
      }
    }


  }
}
