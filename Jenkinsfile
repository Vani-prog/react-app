pipeline {
  agent any
  triggers { cron('* * * * *') }

  environment {
    IMAGE = "vanireddy2025/react-app"
    DOCKER_CREDENTIALS_ID = "docker-creds"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Vani-prog/react-app'
      }
    }

    stage('Install & Build') {
      steps {
        sh 'npm ci'
        sh 'npm run build'
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          SHORT_COMMIT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          IMAGE_TAG = "${env.IMAGE}:${SHORT_COMMIT}"
          sh "docker build -t ${IMAGE_TAG} ."
        }
      }
    }

    stage('Push to Docker Registry') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, usernameVariable: 'vanireddy2025', passwordVariable: '@Krishna2025')]) {
            sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
            sh "docker push ${IMAGE_TAG}"
            sh "docker tag ${IMAGE_TAG} ${env.IMAGE}:latest || true"
            sh "docker push ${env.IMAGE}:latest || true"
          }
        }
      }
    }
  }

  post {
    success { echo 'Pipeline succeeded' }
    failure { echo 'Pipeline failed' }
  }
}
