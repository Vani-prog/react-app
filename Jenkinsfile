pipeline {
  agent any
  

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install / Test / Build') {
      agent { docker { image 'node:18' args '--user root:root' } }
      steps {
        sh 'npm ci'
        sh 'CI=true npm test -- --watchAll=false'
        sh 'npm run build'
      }
      post {
        always { archiveArtifacts artifacts: 'build/**', fingerprint: true }
      }
    }

    stage('Docker: build & push') {
      agent any
      steps {
        script {
          SHORT_COMMIT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          IMAGE_TAG = "${params.REGISTRY}/${params.IMAGE_NAME}:${SHORT_COMMIT}"
          withCredentials([usernamePassword(credentialsId: params.DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh "echo \$DOCKER_PASS | docker login ${params.REGISTRY.split('/')[0]} -u \$DOCKER_USER --password-stdin"
            sh "docker build -t ${IMAGE_TAG} ."
            sh "docker push ${IMAGE_TAG}"
            sh "docker tag ${IMAGE_TAG} ${params.REGISTRY}/${params.IMAGE_NAME}:latest || true"
            sh "docker push ${params.REGISTRY}/${params.IMAGE_NAME}:latest || true"
          }
        }
      }
    }

    stage('Deploy to Swarm') {
      steps {
        script {
          withCredentials([sshUserPrivateKey(credentialsId: params.SWARM_SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
            sh """
              chmod 600 ${SSH_KEY}
              ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ${SSH_USER}@${params.SWARM_HOST} \\
                "docker service update --image ${params.REGISTRY}/${params.IMAGE_NAME}:${SHORT_COMMIT} \\
                 \$(docker service ls --filter name=${params.SWARM_STACK_NAME}_ -q) \\
                 || docker stack deploy -c /path/to/docker-compose.yml ${params.SWARM_STACK_NAME}"
            """
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
```// filepath: /home/mca1/react-app/Jenkinsfile
pipeline {
  agent any
  parameters {
    string(name: 'REGISTRY', defaultValue: 'registry.hub.docker.com/youruser', description: 'Docker registry (host/user)')
    string(name: 'IMAGE_NAME', defaultValue: 'react-app', description: 'Docker image name')
    string(name: 'SWARM_HOST', defaultValue: 'swarm-manager.example.com', description: 'Swarm manager host')
    string(name: 'SWARM_STACK_NAME', defaultValue: 'react_stack', description: 'Docker stack name')
    string(name: 'DOCKER_CREDENTIALS_ID', defaultValue: 'docker-creds', description: 'Jenkins credentials id for docker registry (username/password)')
    string(name: 'SWARM_SSH_CREDENTIALS_ID', defaultValue: 'swarm-ssh-key', description: 'Jenkins SSH private key credentials id for swarm manager')
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install / Test / Build') {
      agent { docker { image 'node:18' args '--user root:root' } }
      steps {
        sh 'npm ci'
        sh 'CI=true npm test -- --watchAll=false'
        sh 'npm run build'
      }
      post {
        always { archiveArtifacts artifacts: 'build/**', fingerprint: true }
      }
    }

    stage('Docker: build & push') {
      agent any
      steps {
        script {
          SHORT_COMMIT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          IMAGE_TAG = "${params.REGISTRY}/${params.IMAGE_NAME}:${SHORT_COMMIT}"
          withCredentials([usernamePassword(credentialsId: params.DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh "echo \$DOCKER_PASS | docker login ${params.REGISTRY.split('/')[0]} -u \$DOCKER_USER --password-stdin"
            sh "docker build -t ${IMAGE_TAG} ."
            sh "docker push ${IMAGE_TAG}"
            sh "docker tag ${IMAGE_TAG} ${params.REGISTRY}/${params.IMAGE_NAME}:latest || true"
            sh "docker push ${params.REGISTRY}/${params.IMAGE_NAME}:latest || true"
          }
        }
      }
    }

    stage('Deploy to Swarm') {
      steps {
        script {
          withCredentials([sshUserPrivateKey(credentialsId: params.SWARM_SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
            sh """
              chmod 600 ${SSH_KEY}
              ssh -o StrictHostKeyChecking=no -i ${SSH_KEY} ${SSH_USER}@${params.SWARM_HOST} \\
                "docker service update --image ${params.REGISTRY}/${params.IMAGE_NAME}:${SHORT_COMMIT} \\
                 \$(docker service ls --filter name=${params.SWARM_STACK_NAME}_ -q) \\
                 || docker stack deploy -c /path/to/docker-compose.yml ${params.SWARM_STACK_NAME}"
            """
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
