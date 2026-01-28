pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Vani-prog/react-app'
            }
        }

        stage('Install & Build') {
            steps {
                dir('my-react-app') {
                    sh '''
                      npm ci
                      npm run build
                    '''
                }
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'docker build -t vani/react-app:latest .'
            }
        }

        stage('Push to Docker Registry') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'vanireddy2025',
                    passwordVariable: '@Krishna2025'
                )]) {
                    sh '''
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                      docker push vani/react-app:latest
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed'
        }
    }
}
