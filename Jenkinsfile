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
                dir('react-app') {
                    sh '''
                      npm ci
                      npm run build
                    '''
                }
            }
        }

        stage('Push to Docker Registry') {
            steps {
                        withCredentials([usernamePassword(credentialsId: 'dockerhubID',
                        usernameVariable: 'vanireddy2025',
                        passwordVariable: '@Krishna2025')]) {
                    
                        sh '''
                          echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                        '''
            }

            }
        }
         stage('Build Docker image') {
            steps {
                sh 'docker build -t vani/react-app:latest .'
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed'
        }
    }
}
