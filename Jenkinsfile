pipeline {
  agent any

  environment {
    NAME = "solar-system"
    VERSION = "${env.BUILD_ID}-${env.GIT_COMMIT}"
    IMAGE_REPO = "prashanth2paramaah"
    GITHUB_TOKEN = credentials('github_token1')

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
    stage('Push Image to Docker Registry') {
      steps {
        script {
                withCredentials([string(credentialsId: 'docker_hub', variable: 'dockerhub_passwd')]) {
                sh 'docker login -u prashanth2paramaah -p $dockerhub_passwd'
                sh 'docker push ${IMAGE_REPO}/${NAME}:${VERSION}'
                }
        }
      }
    }

    stage('Clone/Pull Git-Repo') {
      steps {
        script {
          if (fileExists('gitops-argocd')) {

            echo 'Cloned repo already exists - Pulling latest changes'

            dir("gitops-argocd") {
              sh 'git pull'
            }

          } else {
            echo 'Repo does not exists - Cloning the repo'
            sh 'git clone -b feature4 https://github.com/Prashanth2Paramaah/gitops-argocd.git'
          }
        }
      }
    }
     stage('Update Manifest with New Image') {
      steps {
        dir("gitops-argocd/jenkins-demo") {
          sh 'sed -i "s#prashanth2paramaah.*#${IMAGE_REPO}/${NAME}:${VERSION}#g" deployment.yaml'
          sh 'cat deployment.yaml'
        }
      }
    }
    stage('Commit,Merge and Push the code') {
      steps {
        dir("gitops-argocd/jenkins-demo") {
          sh "git config --global user.email 'jenkins@ci.com'"
          sh 'git remote set-url origin https://$GITHUB_TOKEN@github.com/Prashanth2Paramaah/gitops-argocd.git'
          sh 'git checkout feature4'
//           sh 'git merge feaure2'
          sh 'git add -A'
          sh 'git commit -am "Updated image version for Build - $VERSION"'
          sh 'git checkout main'
          sh 'git merge feature4'
//           sh 'git push origin feature2'
          sh 'git push origin main'
        }
      }
    }

  }
post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "itsprashanth327@gmail.com";  
		}
	}
} 
