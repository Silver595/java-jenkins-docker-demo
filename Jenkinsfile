pipeline {
  agent any

  environment {
    IMAGE_NAME = "yourdockerhubusername/java-demo"
    IMAGE_TAG  = "${env.BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build (Maven)') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }

    stage('Build Docker image') {
      steps {
        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
      }
    }

    stage('Docker Login & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
          sh 'echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin'
          sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
          sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest || true"
          sh "docker push ${IMAGE_NAME}:latest || true"
        }
      }
    }
  }
}
